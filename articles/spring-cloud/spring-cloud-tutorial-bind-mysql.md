---
title: Come associare un'istanza di Database di Azure per MySQL all'applicazione Azure Spring Cloud
description: Informazioni su come associare un'istanza di Database di Azure per MySQL all'applicazione Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 6f2c9a46d8b17a53d2dc43c0c8313c9357f502a1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142166"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Associare un'istanza di database di Azure per MySQL all'applicazione Azure Spring Cloud 

Azure Spring Cloud consente di associare automaticamente determinati servizi di Azure alle applicazioni, invece di configurare manualmente l'applicazione Spring Boot. Questo articolo illustra come associare l'applicazione all'istanza di Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

* Un'istanza di Azure Spring Cloud distribuita
* Un account di Database di Azure per MySQL
* Interfaccia della riga di comando di Azure

Se non è ancora stata distribuita un'istanza di Azure Spring Cloud, seguire le istruzioni riportate in [Avvio rapido: Avviare un'applicazione Azure Spring Cloud usando il portale di Azure](spring-cloud-quickstart-launch-app-portal.md) per distribuire la prima app Spring Cloud.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Associare l'app all'istanza di database di Azure per MySQL

1. Prendere nota del nome utente e della password dell'amministratore dell'account del database di Azure per MySQL. 

1. Connettersi al server, creare un database denominato **testdb** da un client MySQL e quindi creare un nuovo account non amministratore.

1. Aggiungere la dipendenza seguente al file *pom.xml* del progetto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Nel file *application.properties* rimuovere tutte le proprietà `spring.datasource.*`.

1. Aggiornare la distribuzione corrente usando `az spring-cloud app update` oppure crearne una nuova per questa modifica usando `az spring-cloud app deployment create`.  Questi comandi aggiornano o creano l'applicazione con la nuova dipendenza.

1. Nella pagina del servizio **Azure Spring Cloud**, cercare il dashboard nel portale di Azure, cercare il **Dashboard dell'applicazione** e quindi selezionare l'applicazione da associare all'istanza di database di Azure per MySQL.  Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente. 

1. Selezionare **Service binding** (Associazione al servizio), quindi selezionare il pulsante **Create service binding** (Crea associazione al servizio). 

1. Compilare il modulo, assicurarsi di selezionare **Azure MySQL** come **Tipo di binding** usando lo stesso nome di database usato in precedenza e gli stessi valori di nome utente e password annotati nel primo passaggio.

1. Riavviare l'app. L'associazione dovrebbe ora risultare funzionante.

1. Per assicurarsi che l'associazione al servizio sia corretta, selezionarne il nome e controllarne i dettagli. Il campo `property` dovrà essere simile al seguente:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come associare l'applicazione Azure Spring Cloud a un'istanza di Database di Azure per MySQL. Per altre informazioni sul binding di servizi a un'applicazione, vedere [Associare un database Azure Cosmos DB a un'applicazione Azure Spring Cloud](spring-cloud-tutorial-bind-cosmos.md).
