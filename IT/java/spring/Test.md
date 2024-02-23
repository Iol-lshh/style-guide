# 테스트
- [TDD 참조](../../practice/tdd.md)
## 테스트의 종류
1. [Unit test](#unit-test)
    - [JUnit](#junit)
2. [Integration Test](#integration-test)
    - [SpringBootTest](#springboottest)
3. [E2E Test](#e2e-test)
    - [WebMvcTest](#webmvctest)

## 테스트 고려사항
1. deploy the complete application and test
    - jar/war 파일을 빌드하고 어딘가 배포해서 테스트
    - 시스템 테스트 System Testing, 통합 테스트 Integration Testing
2. Test specific units of application code independently
    - 애플리케이션 코드의 특정 단위를 독립적으로 테스트
        - 특정 메서드, 메서드 그룹
    - 단위 테스트 Unit Testing
    - Java 인기 테스트 프레임워크
        - JUnit 단위 테스트 프레임워크
            - JUnit 5 Jupiter test
        - Mockito 모킹 프레임워크

# Unit test
## JUnit
- failure가 없으면 success한다.
    - 여러 조건 Assert를 검사하고, 하나의 검사라도 실패하면 유닛테스트에 실패한다.

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.BDDMockito;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import java.util.Collections;
import java.util.List;

@ExtendWith(MockitoExtension.class)
public class BcSlppStuServiceQueryUnitTest {

    private final String MAPPER_NAMESPACE = "api.v1.bc.slpp.stu.";
    @Mock
    private CommonDao commonDao;
    @InjectMocks
    private BcSlppStuService bcSlppStuService;

    public BcSlppDtoPagingRequest getValidSample(){
        return BcSlppDtoPagingRequest.create4SimpleRequest(
                "tcrUsr0001",
                "stu1",
                10,
                0
        );
    }
    public BcSlppDto getStub(){
        return new BcSlppDto();
    }

    public BcSlppDtoPagingRequest getValidSample4Search(){
        return BcSlppDtoPagingRequest.create4SearchRequest(
                "tcrUsr0001",
                "stu1",
                10,
                0,
                "test"
        );
    }

    // 심플 성공
    @Test
    public void selectSlppListTest_success(){
        BcSlppDtoPagingRequest dto = getValidSample();
        List<BcSlppDto> stub = Collections.singletonList(getStub());

        BDDMockito.given(commonDao.selectList(MAPPER_NAMESPACE + "selectSlppList", dto))
                .willReturn(Collections.singletonList(getStub()));

        List<BcSlppDto> result = bcSlppStuService.selectSlppList(dto);

        Assertions.assertEquals(stub, result);
    }

    // 심플 검색 성공
    @Test
    public void selectSlppListBySearchTest_success(){
        BcSlppDtoPagingRequest dto = getValidSample4Search();
        List<BcSlppDto> stub = Collections.singletonList(getStub());

        BDDMockito.given(commonDao.selectList(MAPPER_NAMESPACE + "selectSlppList", dto))
                .willReturn(Collections.singletonList(getStub()));

        List<BcSlppDto> result = bcSlppStuService.selectSlppList(dto);

        Assertions.assertEquals(stub, result);
    }
}
```

# Integration Test
## SpringBootTest
- 실제 환경(의존성, 인프라 등)을 구성하여 테스트
    - DB를 인메모리 DB 활용 가능
        - H2
    - TestContainer를 활용
- 동시성 테스트를 이곳에서 진행 가능하다.

```java
import org.junit.jupiter.api.*;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.stream.IntStream;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
public class OrderItem1ServiceImplIntegrationTest {

    @Autowired
    OrderItem1Service orderItem1Service;
    @Autowired
    PointService pointService;
    @Autowired
    Payment1Service payment1Service;

    static long orderId = 0L;

    private List<RequestProductDto> prepareRequestPurchaseDtoList() {
        // 30
        RequestProductDto request1 = new RequestProductDto()
                .setProductId(1L)
                .setCount(1);
        // 5
        RequestProductDto request2 = new RequestProductDto()
                .setProductId(2L)
                .setCount(2);
        // 객체 필드를 채우세요
        return Arrays.asList(request1, request2);
    }

    @Test
    @Order(1)
    @DisplayName("구매 성공")
    void purchase() throws Exception {
        long testUserId = 1L;
        long testOrderId = ++orderId;
        List<RequestProductDto> requests = prepareRequestPurchaseDtoList();
        payment1Service.exchange(testUserId, 40);
        // Act
        System.out.println("주문 전 남은 잔액: "+pointService.countRemain(testUserId));
        List<OrderItem> result = orderItem1Service.orderEachProduct(testUserId, testOrderId, requests);
        System.out.println("주문 후 남은 잔액: "+pointService.countRemain(testUserId));

        // Assert
        assertNotNull(result);
        assertEquals(2, result.size());
        assertTrue(pointService.countRemain(testUserId) >= 0);
        //100 - 40 = 60
    }

    // 동시성 테스트 - 주문 실패 케이스 - 포인트 부족
    @Test
    @Order(2)
    @DisplayName("동시성 - 포인트 부족-하나만 실패")
    void orderPointlessWithConcurrentTest() throws Exception {
        long testUserId = 1L;   // 60
        //60 - 40(성공) - 40(실패)

        orderId++;
        ExecutorService executorService = Executors.newFixedThreadPool(4);
        IntStream.range(0, 4)
                .forEach(i ->
                        executorService.submit(() -> {
                            long testOrderId = orderId + i;
                            try {
                                orderItem1Service.orderEachProduct(testUserId, testOrderId, prepareRequestPurchaseDtoList());
                            } catch (Exception e) {
                                System.out.println(i + " 주문 실패!");
                                System.out.println(e.getMessage());
                            }
                            System.out.println(i + " 남은 잔액: "+pointService.countRemain(testUserId));
                        })
                );
        executorService.awaitTermination(1, TimeUnit.SECONDS);
        System.out.println("남은 잔액: "+pointService.countRemain(testUserId));
        assertTrue(pointService.countRemain(testUserId) >= 0);
    }


    @Test
    @DisplayName("인기 조회")
    public void testFavorite() {
        List<ProductDto> favoriteResult = orderItem1Service.favorite(5);

        favoriteResult
                .forEach(productDto -> System.out.println(productDto.id()));
    }
}
```

# E2E Test
## WebMvcTest
- 컨트롤러 레이어를, 주변 설정을 Mock으로 지원하여 가벼운 엔드포인트 유효성 테스트 지원
- `@AutoConfigureMockMvc`
    - Mock으로 MVC 설정을 구성해준다.
- `@WebMvcTest(Class clazz)`
    - MVC 테스트를 제공해준다.

```java

import com.fasterxml.jackson.databind.JavaType;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import java.util.Arrays;
import java.util.List;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyLong;
import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@AutoConfigureMockMvc
@WebMvcTest(OrderController.class)
public class OrderControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private OrderOrchestratorService orderService;

    private static final ObjectMapper OBJECT_MAPPER = new ObjectMapper();

    @Test
    @DisplayName("주문")
    public void order() throws Exception {
        // given
        OrderDto expectedOrderDto = new OrderDto().id(1L).userId(1L).state(Response.Result.SUCCESS);
        when(orderService.order(anyLong(), any())).thenReturn(expectedOrderDto);

        mockMvc.perform(post("/order/purchase")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("{\"userId\":1,\"requestPurchaseList\":[]}"))
                .andExpect(status().isOk());
        verify(orderService, times(1)).order(anyLong(), any());
    }

    @Test
    @DisplayName("주문 내역 조회")
    public void findOrder() throws Exception {
        // Given
        OrderDto order1 = new OrderDto().id(1L).state( Response.Result.SUCCESS).userId(1L);
        OrderDto order2 = new OrderDto().id(2L).state( Response.Result.SUCCESS).userId(2L);
        List<OrderDto> mockOrderList = Arrays.asList(order1, order2);
        when(orderService.findByUserId(anyLong())).thenReturn(mockOrderList);

        // When
        MvcResult mvcResult = mockMvc.perform(get("/order/all/{userId}", 1L)
                        .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andReturn();

        // Then
        String responseJson = mvcResult.getResponse().getContentAsString();
        JavaType orderDtoJavaType = OBJECT_MAPPER.getTypeFactory().constructCollectionType(List.class, OrderDto.class);
        JavaType resultDtoJavaType = OBJECT_MAPPER.getTypeFactory().constructParametricType(ResultDto.class, orderDtoJavaType);
        ResultDto<List<OrderDto>> result = OBJECT_MAPPER.readValue(responseJson, resultDtoJavaType);
        assertEquals(result.getValue().size(), 2);
        assertEquals(result.getValue().get(0).id(), 1L);
        assertEquals(result.getValue().get(1).id(), 2L);
    }
}
```
