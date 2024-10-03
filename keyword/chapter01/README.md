## <핵심 키워드>
<br>
1. 외래 키
<p>외래 키는 한 테이블의 칼럼이 다른 테이블의 기본 키를 참조할 때 사용된다.

&emsp; - **참조 무결성 보장** : 외래 키가 참조하는 값은 반드시 참조되는 테이블에 존재해야한다.

&emsp; - **NULL 값 허용** : 외래 키 필드가 NULL인 경우, 이는 다른 테이블과 관계가 없음을 의미한다.</p>

<br>
2. 기본 키
<p>기본 키 (Primary Key)는 각 테이블의 행을 고유하게 식별하기 위해 사용되는 특정 컬럼 또는 컬럼의 조합이다.

&emsp; - **유일성** : 테이블 내에서 기본 키로 지정된 컬럼은 반드시 유일한 값을 가져야 한다. >> 동일한 값 존재 불가능

&emsp; - **NULL 값 허용 불가** : 기본 키로 지정된 컬럼은 NULL 값을 가질 수 없다. >> 기본 키의 유일성 위반

&emsp; - **자동 인덱스 생성** : 대부분, 기본 키가 설정된 컬럼에 대해 자동으로 인덱스를 생성한다. >> 검색 속도 향상

&emsp; - **데이터 무결성 보장** : 기본 키는 중복된 데이터를 허용하지 않는다.

&emsp; - **단일 컬럼 또는 다중 컬럼 모두 가능**하다.</p>

<br>
3. ER 다이어그램
<p>ER 다이어그램이란 데이터 구조와 테이블 간의 관계를 시각적으로 표현한 다이어그램이다.

**(1) ER 다이어그램의 주요 구성 요소**

&emsp; - 개체 (Entity) : 데이터베이스에서 관리해야 할 정보의 집합. >> 표현 방식 : 사각형

&emsp; - 속성 (Attribute) : 개체가 가진 특성이나 정보. >> 표현 방식 : 타원형

&emsp; - 관계 (Relationship) : 개체 간의 연관성. >> 표현 방식 : 마름모

**(2) ER 다이어그램의 장점**

&emsp; - 복잡한 데이터베이스 구조를 쉽게 이해할 수 있다.

&emsp; - 데이터베이스 설계 시 요구사항을 명확히 하고 관계를 정의하는 데 유용하다.

&emsp; - 개발자와 비개발자 간의 소통을 원활하게 할 수 있는 커뮤니케이션 도구이다.</p>

<br>
4. 복합 키
<p>복합 키는 두 개 이상의 컬럼을 결합하여 하나의 고유한 식별자로 사용하는 키이다.

&emsp; - 유일성 : 각 컬럼의 개별 값이 아닌, 결합된 값을 기준으로 유일성을 보장한다.

&emsp; - NULL 값 허용 불가 : 복합 키를 구성하는 모든 컬럼은 NULL 값을 가질 수 없다. >> 유일성 보장

**(1) 복합 키의 장점**

&emsp; - 데이터 중복 방지 : 단일 키로는 고유하게 구분할 수 없는 복합적인 데이터를 관리하는 데 유리하다.

&emsp; - 정확한 데이터 모델링 : 다양한 컬럼을 조합함으로써, 데이터의 본질을 더 정확하게 표현할 수 있다.

**(2) 복합 키의 단점**

&emsp; - 복잡성 증가 : 여러 컬럼을 복합 키로 사용하기에, 데이터베이스 관리가 복잡해질 수 있다.

&emsp; - 유지보수 어려움 : 복합 키를 구성하는 컬럼 중 하나가 변경될 경우, 관련된 모든 것에 영향을 줄 수 있다.</p>

<br>
5. 연관관계
<p>연관관계 (Relationship)는 관계형 DB에서 두 개체 간의 관계를 정의하는 개념이며, ER 다이어그램으로 표현된다.

**(1) 관계의 종류**

&emsp; - 일대일 관계 : 각 개체는 다른 개체와 하나의 연결만을 가진다.

&emsp; - 일대다 관계 ( = 다대일 관계) : 하나의 개체가 여러 개의 다른 개체와 연관된다.

&emsp; - 다대다 관계 : 다수의 개체가 다수의 개체와 연관된다.

**(2) 관계의 중요성**

&emsp; - 데이터 무결성 유지 : 연관관계를 통해 관련된 데이터 간의 일관성을 유지할 수 있다.

&emsp; - 복잡한 쿼리 지원 : 관계를 정의함으로써, 복잡한 쿼리를 쉽게 이해하고 작성할 수 있다.</p>

<br>
6. 정규화
<p>정규화는 함수적 종속성에 의해서 테이블을 분해하는 과정이다.

**(1) 정규화의 목적**

&emsp; - 데이터 구조의 안정성을 최대화시킨다.

&emsp; - 중복을 배제하여 삽입, 삭제, 갱신 이상의 발생을 방지한다.

&emsp; - 어떠한 관계라도 데이터베이스 내에서 표현 가능하도록 한다.

&emsp; - 데이터 삽입 시 관계를 재구성할 필요성을 줄인다.

&emsp; - 효과적인 검색 알고리즘을 생성한다.

**(2) 정규화 단계**

비정규 릴레이션 - 1NF - 2NF - 3NF - BCNF - 4NF - 5NF

**(3) 정규화 장점**

&emsp; - 데이터 중복 감소 : 데이터 중복을 최소화하여 저장 공간을 효율적으로 사용한다.

&emsp; - 데이터 무결성 향상 : 데이터의 일관성을 높이고 삽입, 삭제, 갱신 시 무결성을 유지한다.</p>

<br>
7. 반 정규화
<p>반 정규화는 정규화 과정을 역행하여 데이터 중복을 허용하고 성능을 최적화하기 위해 테이블 구조를 조정하는 과정이다.

**(1) 반 정규화의 목적 및 장점**

&emsp; - 성능 향상 : 데이터 조회 속도를 크게 향상시킬 수 있다.

&emsp; - 쿼리 단순화 : 데이터 접근이 용이하다.

&emsp; - 빠른 데이터 접근이 가능하다.

**(2) 반 정규화 단점**

&emsp; - 데이터 중복 : 데이터의 일관성을 유지하는 것이 어렵다.

&emsp; - 저장 공간 낭비 : 중복된 데이터로 인해 저장 공간을 더 많이 사용할 수 있다.</p>