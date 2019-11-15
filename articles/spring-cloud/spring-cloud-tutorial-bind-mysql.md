---
title: Come associare Database di Azure per MySQL all'applicazione Azure Spring Cloud | Microsoft Docs
description: Questo articolo illustra come associare Azure MySQL all'applicazione Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607596"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Esercitazione: Associare i servizi di Azure all'applicazione Azure Spring Cloud: Database di Azure per MySQL

Azure Spring Cloud consente di associare automaticamente determinati servizi di Azure alle applicazioni, invece di configurare manualmente l'applicazione Spring Boot. Questa esercitazione illustra come associare l'applicazione ad Azure MySQL.

## <a name="prerequisites"></a>Prerequisiti

* Un'istanza di Azure Spring Cloud distribuita
* Un account di Database di Azure per MySQL
* Interfaccia della riga di comando di Azure

Se non si ha un'istanza di Azure Spring Cloud distribuita, seguire la procedura descritta in questo [Avvio rapido](spring-cloud-quickstart-launch-app-portal.md) per distribuire la prima app Spring Cloud.

## <a name="bind-azure-database-for-mysql"></a>Associare Database di Azure per MySQL

1. Prendere nota del nome utente e della password dell'amministratore dell'account Azure MySQL. Connettersi al server e creare un database denominato `testdb` da un client MySQL. Creare un nuovo account non amministratore.

1. Aggiungere la dipendenza seguente al file `pom.xml` del progetto

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Rimuovere le proprietà `spring.datasource.*`, se presenti, dal file `application.properties`.

1. Aggiornare la distribuzione corrente usando `az spring-cloud app update` oppure crearne una nuova per questa modifica usando `az spring-cloud app deployment create`.  Questi comandi aggiornano o creano l'applicazione con la nuova dipendenza.

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure. Trovare il **Dashboard dell'applicazione** e selezionare l'applicazione da associare ad Azure MySQL.  Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente. Selezionare quindi `Service binding` e poi il pulsante `Create service binding`. Compilare il modulo, assicurarsi di selezionare il **tipo di binding** `Azure MySQL`, lo stesso nome di database usato in precedenza e gli stessi valori di nome utente e password annotati nel primo passaggio.

1. Riavviare l'app. Il binding dovrebbe funzionare ora.

1. Per verificare se il binding di servizi è corretto, selezionarne il nome e controllarne i dettagli. Il campo `property` dovrà essere simile al seguente:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come associare l'applicazione Azure Spring Cloud a un database MySQL.  Per altre informazioni sulla gestione del servizio Azure Spring Cloud, continuare a leggere l'articolo sull'individuazione del servizio e la registrazione.

> [!div class="nextstepaction"]
> [Informazioni su come abilitare l'individuazione del servizio e le registrazioni usando Spring Cloud Service Registry](spring-cloud-service-registration.md).

