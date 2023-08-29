# `Aggregate route`란?

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/7e4e3cec-3de5-420b-9cd6-cda35ccbc6ac)

<aside>
📢 네트워크에 개별적으로 알려진 여러 개의 작은 서브넷이나 네트워크가 있는 경우 라우팅 테이블이 매우 커지고 관리하기 어려울 수 있습니다.

집계 경로를 사용하면 이러한 소규모 네트워크를 요약하여 라우터는 요약된 주소 블록을 팀원에게 알리고 라우팅 테이블에 필요한 라우팅 항목 수를 줄일 수 있습니다.

</aside>

- `Aggregate route`는 라우팅 테이블 크기를 줄이고 라우팅 효율성을 향상시키기 위해 여러 개의 작은 네트워크를 하나의 큰 네트워크로 요약하는 방법
- 라우팅 정책을 시행하고 네트워크 트래픽을 보다 세밀하게 제어하는 데 사용할 수 있다.

## EX> 네트워크에 서브넷

> 네트워크에 서브넷 192.168.1.0/24, 192.168.2.0/24 및 192.168.3.0/24가 있는 경우 이러한 서브넷을 모두 포함하는 범위 192.168.0.0/16을 나타내는 집계 경로를 만들 수 있습니다. 그러면 이 단일 경로가 다른 라우터에 알려지므로 필요한 라우팅 테이블 항목 수가 줄어듭니다.
> 

## EX> 기술 블로그 참조 (cf) https://techblog.woowahan.com/10600/)

<aside>
📢 → 이 코드에 대한 코드 리뷰

```java
@Service
@Transactional
@RequiredArgsConstructor
public class ReviewRegisterService {

    private final ReviewRepository reviewRepository;
    private final ReviewDeliveryRepository reviewDeliveryRepository;
    private final ReviewMenuRepository reviewMenuRepository;
    private final ReviewImageRepository reviewImageRepository;

    ...

    public void register(ReviewRegisterCommand registerCommand) {
        Order order = orderRepository.findById(registerCommand.getOrderId())
                .orElseThrow(() -> new NoSuchElementException("주문이 존재하지 않습니다."));
        ...

        Review review = new Review(registerCommand.getMemberNumber(), order, registerCommand.getShopId(),
                registerCommand.getContent(), registerCommand.getRating());

        Review savedReview = reviewRepository.save(review);
        saveDeliveryReview(registerCommand, savedReview);
        saveReviewMenus(registerCommand, savedReview);
        saveReviewImages(registerCommand, savedReview);
    }

    private void saveDeliveryReview(ReviewRegisterCommand registerCommand, Review savedReview) {
        ...
        reviewDeliveryRepository.save(reviewDelivery);
    }

    private void saveReviewMenus(ReviewRegisterCommand registerCommand, Review savedReview) {
        ...
        for (ReviewMenuCommand reviewMenuCommand : registerCommand.getReviewMenus()) {
            ...
            ReviewMenu reviewMenu = new ReviewMenu(reviewMenuCommand.getMenuName(), reviewMenuCommand.getMenuReviewType(), reviewMenuCommand.getContent());
            reviewMenuRepository.save(reviewMenu);
        }
    }

    private void saveReviewImages(ReviewRegisterCommand registerCommand, Review savedReview) {
        if (registerCommand.getReviewImages() != null) {
            ...
            saveReviewImage(savedReview, imageInfos);
        }
    }

    private void saveReviewImage(Review savedReview, Map<String, String> imageInfos) {
        for (String name : imageInfos.keySet()) {
            reviewImageRepository.save(new ReviewImage(savedReview.getId(), imageInfos.get(name)));
        }
    }
}
```

`Review 1) reviewMenu나 Delivery도 동일할 것 같은데 같은 라이프사이클을 갖는 애그리거트안에서는 루트 애그리거트를 통해서만 변경이나 조회가 가능하도록 수정하면 좋을 것 같아요.`

`Review 2) 같이 저장된다는 건 같은 Aggregate 내에 있다고 생각해도 좋을 것 같아요.`

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/660601b2-f501-4515-b21a-050ad4fc86b1)

배달의민족 앱에서 리뷰를 작성하실 때를 떠올리시면, 배달 리뷰와 별점을 기록하고, 리뷰 이미지와 추천 메뉴를 작성하는 하나의 플로우가 떠오르실 겁니다. 리뷰를 수정할 때도 이미지와 메뉴에 대한 기록을 함께 수정하고 저장됩니다.

즉, 같이 생성되고 같이 수정, 삭제되는 사이클을 가지고 있는데요. 같은 라이프사이클을 갖는 엔티티를 각각의 repository(reviewRepository, reviewDeliveryRepository, reviewMenuRepository, reviewImageRepository)로 관리하게 되면, 외부에서 엔티티에 직접 접근하여 데이터를 변경할 수 있게 되고, 데이터 일관성이 깨질 수 있다는 것을 알게 되었습니다. 각각의 repository를 통해 데이터가 어디서 변경될지 모르니 프로젝트가 커질수록 불안한 마음이 커질 것이라고 생각했습니다.

따라서 Review를 애그리거트 루트로 설정하여 전체 애그리거트를 관리할 수 있도록 개선했습니다.

```java
@Service
@RequiredArgsConstructor
public class ReviewRegisterService {

    private final ReviewRepository reviewRepository;

    ...

    @Transactional
    public void register(ReviewRegisterCommand registerCommand) {
        ...

        try {
            Order order = orderRepository.findById(registerCommand.getOrderId())
                    .orElseThrow(() -> new NoSuchElementException("주문이 존재하지 않습니다."));

            ...

            Review review = Review.builder()
                    .memberNumber(registerCommand.getMemberNumber())
                    .reviewDelivery(registerCommand.toReviewDelivery())
                    .order(order)
                    .menus(saveReviewMenus(registerCommand))
                    .images(saveReviewImages(registerCommand))
                    .shopId(registerCommand.getShopId())
                    .content(registerCommand.getContent())
                    .rating(registerCommand.getRating())
                    .build();

            reviewRepository.save(review);

            ...
        } catch (Exception exception) {
            ReviewExceptionHandler.handleException(exception);
            throw exception;
        }
    }
}
```

→ **리뷰와 관련된 엔티티들은 애그리거트 루트인 리뷰를 통해서만 변경**할 수 있다.

</aside>
