Представим мир, в котором коронавирус побеждает.
Заражены сотни миллионов человек.
ВОЗ принимает решение создать программное обеспечение для учёта заражённых и выздоровевших людей.
Эти данные надо собрать очень быстро, чтобы успеть оперативно выработать рекомендации для различных регионов.
Комиссия приняла решение что данные о заражённых будут поступать в MQ в формате XML.
Хранение данных в БД.
Поступили следующие бизнес-требования:

#### Заражённый имеет следующие характеристики:

- пол (обяз.)
- дата рождения
- идентификатор ДУЛ (уникальный)
- состояние (бессимптомное, удовлетворительное, критическое, смерть и т.п.) (обяз.)
- статус (болен, идёт на поправку, стационар, выздоровел и т.п.) (обяз.)
- страна (обяз.)
- регион (обяз.)
- город (обяз.)
- телефон
- email
- предполагаемая дата заражения
- дата обращения в мед. учреждение (обяз.)

#### Должна быть возможность изменить у заражённого статус и состояние (для этого предусмотрен отдельный тип сообщения).

Пример сообщения. Контейнера может не быть, в этом случае в `Body` будет один документ.

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <Envelope xmlns="http://www.w3.org/2001/06/soap-envelope">
     <Header>
         <Routing>
             <EnvelopeID>3afa8cef-52ba-4d48-925f-1098cf3a14b4</EnvelopeID>
             <Sender>[SenderMQAddress]</Sender>
             <Receiver>[ReceiverMQAddress]</Receiver>
             <DateTime>2020-05-14T13:37:27</DateTime>
         </Routing>
         <MessageInfo>
             <MessageType>MED.00000</MessageType>
         </MessageInfo>
         <MedicalHeader>
             <MedicalID>111111111111</MedicalID>
         </MedicalHeader>
     </Header>
     <Body>
         <Container documentMode="000">
             <DocumentID>12457898-1111-4d48-925f-1098cf3a14b4</DocumentID>
             <Doc>
                 <InfectedInfo documentMode="001">
                     <DocumentID>3afa8cef-1111-4d48-925f-1098cf3a14b4</DocumentID>
                     <Gender>MALE</Gender>
                     <BirthDate>24.07.1962</BirthDate>
                     <DULID>11112222333344</DULID>
                     <HealthStatus>CRITICAL</HealthStatus>
                     <InfectedStatus>SICK</InfectedStatus>
                     <RegionInfo>
                         <CountryCode>804</CountryCode>
                         <Region>Киев</Region>
                         <City>Киев</City>
                     </RegionInfo>
                     <Phone>+79001111113</Phone>
                     <InfectedDate>05.05.2020</InfectedDate>
                     <ApplicationDate>10.05.2020</ApplicationDate>
                 </InfectedInfo>
             </Doc>
             <Doc>
                 <InfectedInfo documentMode="001">
                     <DocumentID>3afa8cef-2222-4d48-925f-1098cf3a14b4</DocumentID>
                     <Gender>FEMALE</Gender>
                     <BirthDate>11.02.1978</BirthDate>
                     <DULID>222233336666</DULID>
                     <HealthStatus>CRITICAL</HealthStatus>
                     <InfectedStatus>SICK</InfectedStatus>
                     <RegionInfo>
                         <CountryCode>804</CountryCode>
                         <Region>Киев</Region>
                         <City>Киев</City>
                     </RegionInfo>
                     <Email>some@mail.com</Phone>
                     <InfectedDate>30.04.2020</InfectedDate>
                     <ApplicationDate>07.05.2020</ApplicationDate>
                 </InfectedInfo>
             </Doc>
             <Doc>
                 <RecoveredInfo documentMode="002">
                     <DocumentID>3afa8cef-3333-4d48-925f-1098cf3a14b4</DocumentID>
                     <PatientID>88888888-4444-1111-925f-1098cf3a14b4</PatientID>
                     <HealthStatus>OK</HealthStatus>
                     <InfectedStatus>RECOVERED</InfectedStatus>
                     <RecoverуDate>14.05.2020</RecoverуDate>
                 </RecoveredInfo>
             </Doc>
         </Container>
     </Body>
 </Envelope>
```

Пример ответа на сообщение выше.

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <Envelope xmlns="http://www.w3.org/2001/06/soap-envelope">
     <Header>
         <Routing>
             <EnvelopeID>11223344-6654-4d48-4578-1098cf3a14b4</EnvelopeID>
             <InitialEnvelopeID>3afa8cef-52ba-4d48-925f-1098cf3a14b4</InitialEnvelopeID>
             <Sender>[SenderMQAddress]</Sender>
             <Receiver>[ReceiverMQAddress]</Receiver>
             <DateTime>2020-05-14T13:37:29</DateTime>
         </Routing>
         <MessageInfo>
             <MessageType>APP.00000</MessageType>
         </MessageInfo>
     </Header>
     <Body>
        <Container documentMode="000">
            <DocumentID>98653247-1111-4d48-925f-1098cf3a14b4</DocumentID>
            <InitialDocumentID>12457898-1111-4d48-925f-1098cf3a14b4</InitialDocumentID>
            <Doc>
                <RegisteredInfo documentMode="003">
                    <InitialDocumentID>3afa8cef-1111-4d48-925f-1098cf3a14b4</InitialDocumentID>
                    <PatientID>e6n9d36g-re63-fd4g-925f-1098cf3a14b4</PatientID>
                </RegisteredInfo>
            </Doc>
            <Doc>
                <RegisteredInfo documentMode="003">
                    <InitialDocumentID>3afa8cef-2222-4d48-925f-1098cf3a14b4</InitialDocumentID>
                    <PatientID>e6n9d36g-re63-fd4g-0045-1098cf3a14b4</PatientID>
                </RegisteredInfo>
            </Doc>
            <Doc>
                <UpdatedInfo documentMode="004">
                    <InitialDocumentID>3afa8cef-3333-4d48-925f-1098cf3a14b4</InitialDocumentID>
                    <UpdatedStatus>OK</UpdatedStatus>
                </UpdatedInfo>
            </Doc>
        </Container>
     </Body>
 </Envelope>
```

Заражённые могут контактировать между собой. Для таких случаев предусмотрен отдельный тип сообщения, который содержит идентификаторы пациентов, с которыми был контакт.
При получении такого сообщения должна быть организована рассылка всем этим пациентам (по e-mail и/или sms) о том что они потенциально имели контакт с заражённым человеком и рекомендацией обратиться в мед. учреждение (не обязательно слать реальным получателям, достаточно сделать заглушку для последующей реализации).
Текст сообщения должен быть легко редактируемым в конфигурации приложения.

Все пациенты, у которых указан ID, должны быть помечены как имевшие контакт с заражённым с сохранением идентификатора заражённого. Связь ManyToMany.

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <Envelope xmlns="http://www.w3.org/2001/06/soap-envelope">
     <Header>
         <Routing>
             <EnvelopeID>55886699-6654-4d48-4578-1098cf3a14b4</EnvelopeID>
             <Sender>[SenderMQAddress]</Sender>
             <Receiver>[ReceiverMQAddress]</Receiver>
             <DateTime>2020-05-14T13:40:29</DateTime>
         </Routing>
         <MessageInfo>
             <MessageType>WHO.00000</MessageType>
         </MessageInfo>
     </Header>
     <Body>
        <InfectedContactInfo documentMode="005">
            <DocumentID>3afa8cef-2389-4d48-925f-1098cf3a14b4</DocumentID>
            <MainPatientID>e6n9d36g-0000-0000-0000-1098cf3a14b4</MainPatientID>
            <PatientID>e6n9d36g-1111-1111-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-2222-2222-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-3333-fd4g-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-4444-fd4g-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-5555-fd4g-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-6666-fd4g-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-7777-fd4g-925f-1098cf3a14b4</PatientID>
            <PatientID>e6n9d36g-8888-fd4g-925f-1098cf3a14b4</PatientID>
            <PotentialContact>
            	<Name>John S.</Name>
            	<Phone>+79001111111</Phone>
            </PotentialContact>
            <PotentialContact>
            	<Name>John A.</Name>
            	<Email>johna@mail.ru</Email>
            </PotentialContact>
            <PotentialContact>
            	<Name>John B.</Name>
            	<Phone>+79001111112</Phone>
            	<Email>johnb@mail.ru</Email>
            </PotentialContact>
            <PotentialContact>
            	<Name>John C.</Name>
            	<Email>johnc@mail.ru</Email>
            </PotentialContact>
        </InfectedContactInfo>
     </Body>
 </Envelope>
```

Пример ответного сообщения:

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <Envelope xmlns="http://www.w3.org/2001/06/soap-envelope">
     <Header>
         <Routing>
             <EnvelopeID>23458978-6654-4d48-4578-1098cf3a14b4</EnvelopeID>
             <InitialEnvelopeID>55886699-6654-4d48-4578-1098cf3a14b4</InitialEnvelopeID>
             <Sender>[SenderMQAddress]</Sender>
             <Receiver>[ReceiverMQAddress]</Receiver>
             <DateTime>2020-05-14T13:40:30</DateTime>
         </Routing>
         <MessageInfo>
             <MessageType>APP.00003</MessageType>
         </MessageInfo>
     </Header>
     <Body>
        <InfectedContactResult documentMode="006">
        	<DocumentID>3afa8cef-2389-0578-df3s-1098cf3a14b4</DocumentID>
            <InitialDocumentID>3afa8cef-2389-4d48-925f-1098cf3a14b4</InitialDocumentID>
            <InformStatus>
            	<PatientID>e6n9d36g-1111-1111-925f-1098cf3a14b4</PatientID>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-2222-2222-925f-1098cf3a14b4</PatientID>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-3333-fd4g-925f-1098cf3a14b4</PatientID>
            	<Status>NOT_AVAILABLE</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-4444-fd4g-925f-1098cf3a14b4</PatientID>
            	<Status>NOT_AVAILABLE</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-5555-fd4g-925f-1098cf3a14b4</PatientID>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-6666-fd4g-925f-1098cf3a14b4</PatientID>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-7777-fd4g-925f-1098cf3a14b4</PatientID>
            	<Status>NOT_AVAILABLE</Status>
            </InformStatus>
            <InformStatus>
            	<PatientID>e6n9d36g-8888-fd4g-925f-1098cf3a14b4</PatientID>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<Phone>+79001111111</Phone>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<Email>johna@mail.ru</Email>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<Phone>+79001111112</Phone>
            	<Status>NOT_AVAILABLE</Status>
            </InformStatus>
            <InformStatus>
            	<Email>johnb@mail.ru</Email>
            	<Status>INFORMED</Status>
            </InformStatus>
            <InformStatus>
            	<Email>johnc@mail.ru</Email>
            	<Status>INFORMED</Status>
            </InformStatus>
        </InfectedContactResult>
     </Body>
 </Envelope>
```

Типы входящих сообщений:

`MED.00000` - сообщение содержит контейнер с разнородными документами

`MED.00001` - сообщение содержит документ `InfectedInfo`

`MED.00002` - сообщение содержит документ `RecoveredInfo`

`WHO.00000` - сообщение содержит документ `InfectedContactInfo`

Типы исходящих сообщений:

`APP.00000` - сообщение содержит ответы на каждый документ из контейнера

`APP.00001` - сообщение содержит документ `RegisteredInfo`

`APP.00002` - сообщение содержит документ `UpdatedInfo`

`APP.00003` - сообщение содержит документ `InfectedContactResult`

Модель данных разработайте самостоятельно. При возникновении спорных ситуаций и там где нет подробных требований пользуйтесь здравым смыслом.

Постарайтесь достичь максимально возможной скорости обработки сообщений (отталкивайтесь от цифры 50 tps), пишите понятный и поддерживаемый код, используйте минимум 2 паттерна проектирования и не пренебрегайте тестами.

Спроектируйте и реализуйте RESTful API (формат данных для обмена `json`) чтобы ВОЗ мог получать статистику по заражённым (всего, по регионам, по полу, по возрасту, по состоянию здоровья, выздоровевших, комбинированные фильтры и т.п.) и выработать рекомендации.

Вы можете использовать фреймворки / библиотеки, если хотите, но не переусердствуйте.
Окончательный результат должен быть исполняемым как отдельная программа (не требует предварительно установленного контейнера / сервера).
Продемонстрируйте с помощью тестов, что API работает должным образом.

Стек:
- Spring Boot, Data
- JMS
- In-memory ActiveMQ
- In-memory DB (реляц.)
- XML
- XSD
- Hibernate
- REST

Удачи!
