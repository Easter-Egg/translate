# Amazon Kinesis Streams Key Concepts ([원문](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html))
아마존 키네시스 스트림을 이용하기 전에, 다음 구조와 용어들을 이해하는 것이 효율적일 것입니다.

### Streams High-level Architecture
다음 그림은 스트림의 고차원적인 구조를 묘사하고 있습니다. 생산자들은 끊임없이 데이터를 스트림으로 보내고 소비자들은 실시간으로 데이터를 처리합니다. 소비자들은 그 결과를 AWS 서비스를 이용하여 다이나모디비, 레드시프트, S3에 저장할 수 있습니다.
![high-level architecture](http://docs.aws.amazon.com/streams/latest/dev/images/architecture.png)

### Streams Terminology
#### Amazon Kinesis Streams
아마존 키네시스 스트림은 정렬된 데이터 레코드들의 집합입니다. 스트림의 각 레코드들은 스트림이 할당한 [시퀀스 넘버](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#sequence-number)를 가지고 있습니다. 스트림의 데이터 레코드들은 [샤드](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#shard)에 분류합니다.

#### Data Records
데이터 레코드는 [아마존 키네시스 스트림](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#stream)에 저장되는 데이터의 단위입니다. 데이터 레코드는 [시퀀스 넘버](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#sequence-number), [파티션 키](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#partition-key), 그리고 데이터 객체로 구성되어 있으며, 이는 변하지 않는 일련의 바이트들입니다. 스트림은 어떠한 경우에도 데이터 객체 내의 내용을 변경하거나 접근하지 않습니다. 데이터 객체 크기는 1MB까지 가능합니다.

#### Retention Period(데이터 보유 기간)
시간 데이터 레코드들의 길이는 그들이 스트림에 추가된 후에 접근 할 수 있습니다. 스트림의 데이터 보유 기간은 기본적으로 24시간으로 설정되어 있습니다. 이 기간은 [IncreaseStreamRetentionPeriod](http://docs.aws.amazon.com/kinesis/latest/APIReference/API_IncreaseStreamRetentionPeriod.html) 명령을 통해서 최대 168시간(7일)까지 증가시킬 수 있습니다. 그리고 이 기간을 [DecreaseStreamRetentionPeriod](http://docs.aws.amazon.com/kinesis/latest/APIReference/API_DecreaseStreamRetentionPeriod.html) 명령을 통해 최소 24시간으로 낮출 수도 있습니다. 추가적인 요금은 데이터 보유 기간이 24시간보다 높게 설정 되어 있는 경우에 발생합니다. 더 많은 정보는 [아마존 키네시스 스트림 요금정책](https://aws.amazon.com/ko/kinesis/streams/pricing/)에서 확인할 수 있습니다.

#### Produces
생산자는 아마존 키네시스 스트림에 데이터를 넣습니다. 예를 들어, 스트림으로 로그 데이터를 보내는 웹 서버를 생산자라고 할 수 있습니다.

#### Consumers
소비자는 아마존 키네시스 스트림으로부터 데이터를 가져오고 처리합니다. 이러한 소비자들은 [아마존 키네시스 스트림 어플리케이션](http://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#enabled-application)을 의미합니다.

#### Amazon Kinesis Streams Applications
아마존 키네시스 스트림 어플리케이션은 일반적으로 EC2 인스턴스들 위에서 돌아가는 스트림 소비자들을 의미합니다.

아마존 키네시스 클라이언트 라이브러리 또는 스트림 API를 이용하여 아마존 키네시스 스트림 어플리케이션을 개발할 수 있습니다.

아마존 키네시스 스트림 어플리케이션의 반환 값은 다른 스트림의 입력 값이 될 수 있으며, 이는 데이터를 실시간으로 처리하는 복잡한 아키텍처를 만드는 것을 가능하게 합니다. 또한 어플리케이션은 다른 AWS 서비스들로 데이터를 보내는 것이 가능합니다. 하나의 스트림을 위한 여러 개의 어플리케이션이 존재할 수도 있으며, 각 어플리케이션은 스트림으로부터 온 데이터를 동시에 독립적으로 소비할 수 있습니다.

#### Shards
샤드는 스트림에 있는 데이터 레코드의 고유 식별 그룹을 의미합니다. 스트림을 하나 이상의 샤드로 구성할 수 있으며, 각각 일정한 단위의 가용량을 제공합니다. 각각의 샤드는 읽기에 대해 최대 5개의 트랜잭션을 지원하며 초당 최대 2MB의 데이터를 읽을 수 있습니다. 쓰기의 경우, 초당 1,000개의 데이터를 쓸 수 있으며 파티션 키를 포함하여 최대 1MB의 데이터를 쓸 수 있습니다. 스트림의 데이터 가용량은 스트림에 지정한 샤드 수와 비례합니다. 스트림의 전체 가용량은 스트림 내의 샤드의 가용량의 합과 같습니다.

만약 들어오는 데이터의 양이 증가한다면, 스트림에 할당된 샤드의 수를 늘리거나 줄일 수 있습니다.

#### Partition Keys
파티션 키는 스트림 내의 샤드에 의해 데이터를 분류하는 데 사용됩니다. 스트림 서비스는 주어진 데이터 레코드가 어떤 샤드에 속해 있는 지를 결정하기 위해 각각의 데이터 레코드와 연관된 파티션 키를 이용하여 스트림에 속해 있는 데이터를 여러 개의 샤드들로 데이터 레코드를 분류합니다. 파티션 키는 유니코드 문자열으로써 최대 256 바이트의 길이를 가질 수 있습니다. MD5 해쉬 함수는 파티션 키를 128비트 정수 값에 매핑하고 관련된 데이터 레코드들을 샤드에 매핑하는데 사용합니다. 파티션 키는 데이터를 스트림에 넣는 어플리케이션에 의해 결정됩니다.

#### Sequence Numbers
각 데이터는 특별한 시퀀스 넘버를 가지고 있습니다. 시퀀스 넘버는 <code>client.putRecords</code> 또는 <code>client.putRecord</code>를 통해 스트림에 데이터를 쓴 후에 스트림에 의해 할당 됩니다. 동일한 파티션 키에 대한 시퀀스 넘버는 일반적으로 시간이 지날 수록 증가합니다; 쓰기 요청 사이에 긴 시간이 지나면, 더 큰 시퀀스 넘버가 할당됩니다.
> Note
> 시퀀스 넘버는 같은 스트림 내에 일련의 데이터를 색인자로 사용할 수 없습니다. 데이터 집합은 논리적으로 분리되어 있으며, 파티션 키를 사용하거나 각 데이터 집합을 위한 분리된 스트림을 생성해야 합니다.

#### Amazon kinesis Client Library
아마존 키네시스 클라이언트 라이브러리는 장애 허용을 지원합니다. 아마존 키네시스 클라이언트 라이브러리는 모든 샤드에 대해 동작하는 레코드 프로세서가 있으며 해당 샤드를 처리하고 있음을 보장합니다. 이 라이브러리는 또한 스트림으로부터 데이터를 읽는 것을 간단하게 합니다. 아마존 키네시스 클라이언트 라이브러리는 제어 정보를 저장하기 위해 다이나모디비를 사용합니다. 이는 데이터를 처리하는 어플리케이션당 하나의 테이블을 생성합니다.

#### Application Name
아마존 스트림 어플리케이션의 이름은 각 어플리케이션을 구분합니다. 어플리케이션들은 AWS 계정과 어플리케이션에 의해 사용되는 리전으로 제한된 유일한 이름을 가지고 있습니다. 이 이름은 다이나모디비의 제어 테이블과 클라우드워치 지표를 대한 이름공간을 위한 이름으로 사용됩니다.
