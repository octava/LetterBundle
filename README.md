# LetterBundle

Основная задача модуля - предоставить интерфейс для создания, редактированияи отправки писем. Также сделать удобным создание единого оформления писем и подстановки в них данных.

octava_letter:
    from_name: Example Name         # required
    from_email: mail@example.com    # required
    model_service: ~                # not_required

Админка
 - шаблон layout
 - шаблон письма в БД, плюс выбор layout
 - предпросмотр
 - информация по placeholders
 - код для формирования doctrine:migration
 - отправленные письма
 - отправленные письма архив

Использование

    Создание и отправка письма

        $letterManager = $container->get(...
        $letter = $letterManager->getLetter(<alias>);

        $letter->setResendHash($letterManager->getHashByCookie($letter, $request));   //для анонимного пользователя
        $letter->setResendHash($letterManager->getHashById($client->getId());         //для зарегистрированного пользователя

        $letter->setTo(...);

        $letter->setMethodOne(...);
        $letter->setMethodTwo(...);
        $letter->setMethodThree(...);

        $letter->setPreSendFunction(...);
        $letter->setPostSendFunction(...);

        $letterManager->persist($letter);
        $letterManager->flush();

    Повторная отправка письма
        Каждое письмо сохраняется в БД методом persist, в котором есть уникальный атрибут идентификации пользователя.
        Соответственно, можно отправить письмо заново, снегерировав ссылку при помощи twig функции ```{{ letter_resend(<alias>, $hash) }}``` (где hash $letterManager->getHashByCookie($letter, $request), либо $letterManager->getHashById($client->getId()), переход по ссылке приведет на стандартный actionResend контроллера LetterBundle

Команды
 - перенос писем в архив

Модель письма

Наследуется от Abstract класса и если наследуется интерфейс ContainerAwareInterface, то содержит ссылку на контейнер.
Содержит в себе методы, устанавливающие значения плейсхолдеров.

abstract public function configurePlaceholders(OptionsResolver $resolver);

abstract public function getPlaceholdersDesription();
abstract public function getPlaceholdersData();

public function getPlaceholders() - возвращает результат OptionsResolver::resolve(array $data)
