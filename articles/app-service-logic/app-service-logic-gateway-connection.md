<properties
   pageTitle="Connessione al gateway dati locale delle app per la logica | Microsoft Azure"
   description="Informazioni su come creare una connessione al gateway dati locale da un'app per la logica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# Connessione al gateway dati locale per le app per la logica

I connettori delle app per la logica supportati consentono di configurare la connessione per accedere ai dati locali tramite il gateway dati locale. La procedura seguente illustra come installare e configurare il gateway dati locale per integrarsi con una app per la logica.

## Prerequisiti

* Uso di un indirizzo di posta elettronica aziendale o dell'istituto di istruzione in Azure per associare il gateway dati locale con l'account (account basato su Azure Active Directory)
    * Se si usa un account Microsoft, ad esempio @outlook.com, @live.com, è possibile usare l'account Azure per creare un indirizzo di posta elettronica aziendale o dell'istituito d'istruzione [seguendo questa procedura](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] A causa di un'attuale limitazione, l'installazione del gateway locale verrà completata solo se viene usato un account registrato in Power BI. Per completare correttamente l'installazione, registrare nel frattempo qualsiasi account in "Power BI (gratuito)".

* Gateway dati locale [installato in un computer locale](app-service-logic-gateway-install.md).
* Per il gateway non deve esistere un'attestazione da parte di un altro gateway dati locale di Azure. L'[attestazione viene eseguita con la creazione illustrata nel passaggio 2 di seguito](#2-create-an-azure-on-premises-data-gateway-resource). Un'installazione può essere associata a una sola risorsa gateway.

## Installazione e configurazione della connessione

### 1\. Installare il gateway dati locale

Per informazioni sull'installazione del gateway dati locale, vedere [questo articolo](app-service-logic-gateway-install.md). Prima di procedere con il resto della procedura, è necessario installare il gateway in un computer locale.

### 2\. Creare una risorsa gateway dati locale di Azure

Una volta installata, è necessario associare la sottoscrizione di Azure al gateway dati locale.

1. Accedere ad Azure con lo stesso o indirizzo di posta elettronica aziendale o dell'istituto di istruzione usato durante l'installazione del gateway
1. Fare clic sul pulsante **Nuovo**
1. Cercare e selezionare **Gateway dati locale**
1. Completare le informazioni per associare il gateway al proprio account, selezionando anche il **nome installazione** appropriato

    ![Connessione al gateway dati locale][1]
1. Fare clic sul pulsante **Crea** per creare la risorsa

### 3\. Creare una connessione della app per la logica nella finestra di progettazione

Ora che la sottoscrizione di Azure è associata a un'istanza del gateway dati locale, è possibile creare una connessione ad essa dall'interno di una app per la logica.

1. Aprire una app per la logica e scegliere un connettore che supporta la connettività locale, che al momento della stesura di questo articolo è SQL Server
1. Selezionare la casella di controllo **Connetti tramite gateway dati locale**

    ![Creazione del gateway nella finestra di progettazione della app per la logica][2]
1. Selezionare il **gateway** a cui connettersi e completare eventuali altre informazioni di connessione necessarie
1. Fare clic su **Crea** per creare la connessione

La connessione deve ora essere configurata correttamente per l'uso nella app per la logica.

## Passaggi successivi
- [Esempi e scenari comuni per le app per la logica](app-service-logic-examples-and-scenarios.md)
- [Funzionalità di Enterprise Integration](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG

<!---HONumber=AcomDC_0803_2016-->