---
title: Caricare un certificato dell'API Gestione di Azure | Documentazione Microsoft
description: Informazioni su come caricare il certificato di gestione API per il portale di Azure classico.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: 89c08f2d1e0e244503e3fc4355cad0b4391618e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-an-azure-management-api-management-certificate"></a>Caricare un certificato di gestione dell'API di gestione di Azure
I certificati di gestione consentono di eseguire l'autenticazione con il modello di distribuzione classico fornito da Azure. Molti programmi e strumenti (ad esempio Visual Studio o Azure SDK) usano questi certificati per automatizzare la configurazione e la distribuzione di vari servizi di Azure. 

> [!WARNING]
> Fare attenzione. Questi tipi di certificati consentono a chiunque esegua l'autenticazione di gestire la sottoscrizione a cui sono associati.
>
>

Se servono altre informazioni sui certificati di Azure, compresa la creazione di un certificato autofirmato, vedere [Panoramica sui certificati per i servizi cloud di Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

È anche possibile usare [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) per autenticare il codice client per scopi di automazione.

**Nota:** è necessario essere un co-amministratore della sottoscrizione per eseguire le operazioni con i certificati di gestione. [Altre informazioni](https://go.microsoft.com/fwlink/?linkid=849300) su come aggiungere o rimuovere co-amministratori dal nuovo portale di Azure. 

## <a name="upload-a-management-certificate"></a>Creare un certificato di gestione
Dopo aver creato un certificato di gestione (file con estensione cer solo con la chiave pubblica), è possibile caricarlo nel portale. Quando il certificato è disponibile nel portale, chiunque disponga di un certificato corrispondente (chiave privata) può connettersi tramite l'API di gestione e accedere alle risorse per la sottoscrizione associata.

1. Accedere al [Portale di Azure](http://portal.azure.com).
2. Fare clic su **Altri servizi** in fondo all'elenco dei servizi di Azure e quindi selezionare **Sottoscrizioni** nel gruppo di servizi _Generale_.

    ![Menu Sottoscrizioni](./media/azure-api-management-certs/subscriptions_menu.png)

3. Assicurarsi di selezionare la sottoscrizione corretta a cui si vuole associare il certificato.     
4. Dopo aver selezionato la sottoscrizione corretta, selezionare **Certificati di gestione** nel gruppo _Impostazioni_.

    ![Impostazioni](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Fare clic sul pulsante **Upload** (Carica).

    ![Caricamento nella pagina Certificati](./media/azure-api-management-certs/certificates_page.png)
6. Compilare le informazioni della finestra di dialogo e fare clic su **Carica**.

    ![Impostazioni](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è disponibile un certificato di gestione associato a una sottoscrizione, è possibile (dopo avere installato il certificato corrispondente in locale) connettersi a livello di codice all'[API REST del modello di distribuzione classico](https://msdn.microsoft.com/library/azure/mt420159.aspx) e automatizzare le varie risorse di Azure che possono essere associate a tale sottoscrizione.
