- В какой момент собираем согласие на CDC? После адреса до KYC?
- Мы пользуемся сервисом KAS-backend (url зависит от локации), он отвечает за OTP, мы дергаем
- FE шлет свою jwt в BE в заголовках
- BE шлет в KAS ту же jwt POST /api/sendOtp (авторизация bearer), по этому jwt KAS понимает о чем речь
- Код юзеру приходит по sms после того, как клиент попадаем на экране OTP
- Далее мы отправляем в KAS OTP от юзера POST /api/checkotp { "authcode": 1234 } + jwt
- Если получаем OK от KAS, то записываем в UoD timestamp согласия.
- Переходим на KYC. Нужно ли дождаться решения SEON? При успешном завершении KYC мы смотрим в geoconfig в поле origination_events_enabler, если поле включено, то мы шлем в кафку в топик neobank-own-funding.origination-events.v1.prod сообщение с (user_id, session_id, split_id, step: KYC). Его обрабатывает Inbox. Создается задачка для кроны.
- Переходим на шаг статуса. (Там выполняется проверка сеона). На этом же шаге выполняется поход в CDC.
- Данные берутся из UoD. partnersCircleCredit.CreditReportRequest{ FirstName: userData.Firstname.Value, MiddleName: userData.SecondLastname.Value, LastName: userData.Lastname.Value, DateOfBirth: dateOfBirth, RFC: userData.RFC.Value, Nationality: "MX", Address: partnersCircleCredit.Address{ City: userData.AddressCity.Value, State: userData.AddressState.Value, Municipality: userData.AddressMunicipality.Value, Neighborhood: userData.AddressNeighborhood.Value, Zipcode: userData.AddressZipcode.Value, Street: userData.AddressStreet.Value, House: userData.AddressHouse.Value, Apartment: userData.AddressApartment.Value, }, }. **Должны браться из application.**
- Нужно учитывать сколько живет консент. Перед отправкой запроса в SEON мы смотрим на наличие CDC чека, но не смотрим, что 3 года не прошло. В автоскипе мы не пропускаем шаг credit history, если прошло 3 года. 
- Берем DOB и приводим к определенному формату
- Идем в CDC по API POST /v2/rcc в запросе
  Пэйлоад:
  type rccRequest struct { FirstName string `json:"primerNombre" validate:"required,min=0,max=50,uppercase"` MiddleName string `json:"apellidoMaterno" validate:"required,min=0,max=30,uppercase"` LastName string `json:"apellidoPaterno" validate:"required,min=0,max=30,uppercase"` DateOfBirth string `json:"fechaNacimiento" validate:"required,fmtDateOnly"` RFC string `json:"RFC" validate:"required,rfc"` Nationality string `json:"nacionalidad" validate:"required,len=2,uppercase"` Address address `json:"domicilio" validate:"required"` } type address struct { Address string `json:"direccion" validate:"required,min=0,max=85,uppercase"` Neighborhood string `json:"coloniaPoblacion" validate:"required,min=0,max=65,uppercase"` Municipality string `json:"delegacionMunicipio" validate:"required,min=0,max=65,uppercase"` City string `json:"ciudad" validate:"required,min=0,max=65,uppercase"` State string `json:"estado" validate:"required,min=0,max=4,uppercase"` Zipcode string `json:"CP" validate:"required,len=5,number"` }
- Данные мы трансформируем 
  1) путем замены букв с апофстрофами на буквы без апострофом (Акцент).
2) Маппим стейт в их стейт (в код)
- Если там не ошибка, то мы получаем этот сырой респонс, записываем его в S3, записываем в partners_enrichment, проставляем desicion. 
- Также мы отправляем репорты. Поинтересоваться у Саши. 
- Как устроено взаимодействие с CDC?
- Как мы собираем согласие?
- Как устроен ассинхронный поход в CDC?
- Как работает подписание согласие через OTP?
- Какие данные направляем CDC?
- Что получаем в ответ?
- Как устроен репортинг в CDC?





