---
title: Ricevere un messaggio di posta elettronica quando cambia lo stato di un segreto dell'insieme di credenziali delle chiavi
description: Guida all'uso di App per la logica per rispondere alle modifiche dei segreti di Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 2781ef69ce85e82dab45a9f890ef5e6862949d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087984"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Usare App per la logica per ricevere messaggi di posta elettronica sulle modifiche dello stato dei segreti dell'insieme di credenziali delle chiavi

In questa guida si apprenderà come rispondere agli eventi di Azure Key Vault ricevuti tramite [Griglia di eventi di Azure](../../event-grid/index.yml) usando [App per la logica di Azure](../../logic-apps/index.yml). Al termine, si otterrà un'app per la logica di Azure configurata per l'invio di un messaggio di posta elettronica di notifica ogni volta che viene creato un segreto in Azure Key Vault.

Per una panoramica dell'integrazione tra Azure Key Vault e Griglia di eventi di Azure, vedere [Monitoraggio di Key Vault con Griglia di eventi di Azure](event-grid-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account di un provider di posta elettronica supportato da App per la logica di Azure, come Office 365 Outlook. Questo account di posta elettronica viene usato per inviare le notifiche degli eventi. Per un elenco completo dei connettori per App per la logica supportati, vedere [Panoramica dei connettori](/connectors).
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Un insieme di credenziali delle chiavi nella sottoscrizione di Azure. È possibile creare rapidamente un nuovo insieme di credenziali delle chiavi seguendo la procedura descritta in [Impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure](../secrets/quick-create-cli.md).
- Griglia di eventi registrata come provider di risorse, vedere [Registrazioni dei provider di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

## <a name="create-a-logic-app-via-event-grid"></a>Creare un'app per la logica tramite Griglia di eventi

Per prima cosa, creare un'app per la logica con un gestore griglia di eventi e sottoscrivere gli eventi di Azure Key Vault "SecretNewVersionCreated".

Per creare una sottoscrizione di Griglia di eventi di Azure, seguire questa procedura:

1. Nel portale di Azure passare all'insieme di credenziali delle chiavi, selezionare **Eventi > Attività iniziali** e fare clic su **App per la logica**

    
    ![Insieme di credenziali delle chiavi - pagina Eventi](../media/eventgrid-logicapps-kvsubs.png)

1. In **Progettazione app per la logica** verificare la connessione e fare clic su **Continua** 
 
    ![Progettazione app per la logica - connessione](../media/eventgrid-logicappdesigner1.png)

1. Nella schermata **Quando si verifica un evento della risorsa** seguire questa procedura:
    - Lasciare le impostazioni predefinite di **Sottoscrizione** e **Nome risorsa**.
    - Selezionare **Microsoft.KeyVault.vaults** in **Tipo di risorsa**.
    - Selezionare **Microsoft.KeyVault.SecretNewVersionCreated** in **Event Type Item - 1** (Elemento tipo di evento - 1).

    ![Progettazione app per la logica - gestore dell'evento](../media/eventgrid-logicappdesigner2.png)

1. Selezionare **+ Nuovo passaggio**. Verrà visualizzata una finestra Scegliere un'azione.
1. Cercare **Posta elettronica**. Selezionare il connettore corrispondente al provider di posta elettronica in uso. Questa esercitazione usa **Office 365 Outlook**. I passaggi per altri provider di posta elettronica sono del tutto simili.
1. Selezionare l'azione **Invia un messaggio di posta elettronica (v2)**.

   ![Progettazione app per la logica - Invia messaggio di posta elettronica](../media/eventgrid-logicappdesigner3.png)

1. Creare il modello di messaggio di posta elettronica:
    - **A:** immettere l'indirizzo di posta elettronica per ricevere i messaggi di notifica. Per questa esercitazione, usare un account di posta elettronica a cui è possibile accedere per i test.
    - **Oggetto** e **Corpo**: scrivere il testo del messaggio di posta elettronica. Selezionare le proprietà JSON dallo strumento selettore per includere il contenuto dinamico in base ai dati dell'evento. È possibile recuperare i dati dell'evento usando `@{triggerBody()?['Data']}`.

    Il modello di messaggio di posta elettronica creato sarà simile all'esempio seguente.

    ![Progettazione app per la logica - Corpo messaggio di posta elettronica](../media/eventgrid-logicappdesigner4.png)

8. Fare clic su **Salva con nome**.
9. Immettere un **nome** per la nuova app per la logica e fare clic su **Crea**.
    
    ![Progettazione app per la logica - Crea](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Test e verifica

1.  Passare all'insieme di credenziali delle chiavi nel portale di Azure e selezionare **Eventi > Sottoscrizioni di eventi**.  Verificare che sia stata creata una nuova sottoscrizione
    
    ![Progettazione app per la logica - Test e verifica](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Passare all'insieme di credenziali delle chiavi, selezionare **Segreti** e quindi **+ Genera/Importa**. Creare un nuovo segreto a scopo di test, assegnare un nome alla chiave e mantenere le impostazioni predefinite dei parametri rimanenti.

    ![Insieme di credenziali delle chiavi - creazione del segreto](../media/eventgrid-logicapps-kv-create-secret.png)

1. Nella schermata **Crea un segreto** specificare un nome, un valore e selezionare **Crea**.

Una volta creato il segreto, si riceverà un messaggio di posta elettronica all'indirizzo configurato.

## <a name="next-steps"></a>Passaggi successivi

- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure](event-grid-overview.md)
- Procedura: [Inoltrare le notifiche dell'insieme di credenziali delle chiavi ad Automazione di Azure](event-grid-tutorial.md).
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Altre informazioni su [Griglia di eventi di Azure](../../event-grid/index.yml).
- Altre informazioni sulla [funzionalità App per la logica del Servizio app di Azure](../../logic-apps/index.yml).
