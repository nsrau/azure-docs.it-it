---
title: Distribuire Azure Blockchain Workbench
description: Come distribuire Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/22/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80a40cec8ebd062751e896f9b555c5ed5464d7a3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-azure-blockchain-workbench"></a>Distribuire Azure Blockchain Workbench

Azure Blockchain Workbench viene distribuito usando un modello di soluzione in Azure Marketplace. Il modello semplifica la distribuzione dei componenti necessari per creare applicazioni blockchain. Una volta distribuito, Blockchain Workbench fornisce l'accesso alle app client per creare e gestire utenti e applicazioni blockchain.

Per altre informazioni sui componenti di Blockchain Workbench, vedere [Architettura di Azure Blockchain Workbench](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Azure Blockchain Workbench richiede l'esecuzione di diverse operazioni preliminari prima della distribuzione. Queste operazioni preliminari includono le registrazioni dell'applicazione e della configurazione di Azure AD.

### <a name="blockchain-workbench-api-app-registration"></a>Registrazione dell'app per le API per Blockchain Workbench

Per la distribuzione di Blockchain Workbench, è necessaria la registrazione di un'applicazione Azure AD. Per registrare l'app, è necessario un tenant di Azure Active Directory (Azure AD). È possibile usare un tenant esistente oppure crearne uno nuovo. Se si usa un tenant di Azure AD esistente, sono necessarie autorizzazioni sufficienti per registrare le applicazioni all'interno del tenant di Azure AD. Le registrazioni dell'applicazione devono essere incluse nel tenant dell'amministratore della sottoscrizione in cui viene distribuito Workbench. Per altre informazioni sui tenant di Azure AD, vedere [Come ottenere un tenant di Active Directory](../active-directory/develop/active-directory-howto-tenant.md) e [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'account nell'angolo superiore destro e passare al tenant di Azure AD desiderato. Il tenant deve essere il tenant dell'amministratore della sottoscrizione in cui viene distribuito Workbench ed è necessario avere autorizzazioni sufficienti per registrare le applicazioni.
3. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**. Selezionare **Registrazioni per l'app** > **Registrazione nuova applicazione**.

    ![Registrazione delle app](media/blockchain-workbench-deploy/app-registration.png)

4. Specificare i valori desiderati per **Nome** e **URL accesso** per l'applicazione. È possibile usare valori segnaposto, perché i valori vengono modificati durante la distribuzione. 

    ![Creare la registrazione dell'app](media/blockchain-workbench-deploy/app-registration-create.png)

    |Impostazione  | Valore  |
    |---------|---------|
    |NOME | `Blockchain API` |
    |Tipo di applicazione |App Web/API|
    |URL di accesso | `https://blockchainapi` |

5. Selezionare **Crea** per registrare l'applicazione Azure AD.

### <a name="modify-application-manifest"></a>Modificare il manifesto dell'applicazione

È quindi necessario modificare il manifesto dell'applicazione per l'uso di ruoli applicazione in Azure AD in modo da specificare gli amministratori di Blockchain Workbench.  Per altre informazioni sui manifesti delle applicazioni, vedere [Manifesto dell'applicazione Azure Active Directory](../active-directory/develop/active-directory-application-manifest.md).

1. Per l'applicazione registrata, selezionare **Manifesto** nel riquadro dei dettagli dell'applicazione registrata.
2. Generare un GUID. Per generare un GUID, è possibile usare il comando `[guid]::NewGuid()` di PowerShell o uno strumento online. 
3. È necessario aggiornare la sezione **appRoles** del manifesto. Nel riquadro Modifica manifesto selezionare **Modifica** e sostituire `"appRoles": []` con il codice JSON specificato. Assicurarsi di sostituire il valore per il campo **id** con il GUID generato. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Modificare il manifesto](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Il valore **Amministratore** è necessario solo per identificare gli amministratori di Blockchain Workbench.

4.  Fare clic su **Salva** per salvare le modifiche apportate al manifesto dell'applicazione.

### <a name="add-graph-api-key-to-application"></a>Aggiungere la chiave API Graph all'applicazione

Blockchain Workbench usa Azure AD come sistema di gestione delle identità principale per gli utenti che interagiscono con applicazioni blockchain. Perché Blockchain Workbench possa accedere ad Azure AD e recuperare le informazioni utente, come nomi e indirizzi di posta elettronica, è necessario aggiungere una chiave di accesso. Blockchain Workbench usa la chiave per l'autenticazione con Azure AD.

1. Per l'applicazione registrata, selezionare **Impostazioni** nel riquadro dei dettagli dell'applicazione registrata.
2. Selezionare **Chiavi**.
3. Aggiungere una nuova chiave specificando una descrizione in **Descrizione** per la chiave e scegliendo il valore di durata in **Scadenza**. 

    ![Creare la chiave](media/blockchain-workbench-deploy/app-key-create.png)

    |Impostazione  | Valore  |
    |---------|---------|
    | DESCRIZIONE | `Service` |
    | Expires | Scegliere una scadenza |

4. Selezionare **Salva**. 
5. Copiare il valore della chiave e archiviarlo per i passaggi successivi. Questo valore sarà necessario per la distribuzione.

    > [!IMPORTANT]
    >  Se non si salva la chiave per la distribuzione, sarà necessario generare una nuova chiave. Non è possibile recuperare il valore della chiave dal portale in un secondo momento.

### <a name="add-graph-api-required-permissions"></a>Aggiungere le autorizzazioni dell'API Graph necessarie

L'applicazione API deve richiedere l'autorizzazione dell'utente per accedere alla directory. Impostare l'autorizzazione seguente per l'applicazione API:

1. Nella registrazione dell'app per le API Blockchain selezionare **Impostazioni > Autorizzazioni necessarie > Seleziona un'API > Microsoft Graph**.

    ![Selezionare un'API](media/blockchain-workbench-deploy/client-app-select-api.png)

    Fare clic su **Seleziona**.

2. In **Abilita accesso** in **Autorizzazioni applicazione** scegliere **Leggi i profili completi di tutti gli utenti**.

    ![Abilitare l'accesso](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Fare clic su **Seleziona** e quindi su **Fine**.

3. In **Autorizzazioni necessarie** selezionare **Concedi autorizzazioni** e quindi selezionare **Sì** per la richiesta di verifica.

   ![Concedere le autorizzazioni](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Concedendo l'autorizzazione, si consente a Blockchain Workbench di accedere agli utenti nella directory. L'autorizzazione di lettura è necessaria per cercare e aggiungere membri a Blockchain Workbench.

### <a name="get-application-id"></a>Ottenere l'ID applicazione

Le informazioni su ID e tenant dell'applicazione sono necessarie per la distribuzione. Raccogliere e archiviare le informazioni per usarle durante la distribuzione.

1. Per l'applicazione registrata, selezionare **Impostazioni** > **Proprietà**.
2.  Nel riquadro **Proprietà** copiare e archiviare i valori seguenti per usarli durante la distribuzione.

    ![Proprietà dell'app per le API](media/blockchain-workbench-deploy/app-properties.png)

    | Impostazione da archiviare  | Da usare nella distribuzione |
    |------------------|-------------------|
    | ID applicazione | Azure Active Directory setup (Configurazione di Azure Active Directory) > ID applicazione |

### <a name="create-an-azure-ad-key-vault-application"></a>Creare un'applicazione Key Vault di Azure AD

Per la distribuzione di Blockchain Workbench, è necessaria la registrazione di un'applicazione Key Vault di Azure AD.

1. Nel riquadro di spostamento a sinistra del portale di Azure selezionare il servizio **Azure Active Directory**. Selezionare **Registrazioni per l'app** > **Registrazione nuova applicazione**.
2. Specificare i valori desiderati per **Nome** e **URL accesso** per l'applicazione. È possibile usare valori segnaposto, perché potranno essere modificati successivamente durante la distribuzione.

    ![Creare la registrazione di un'app Key Vault](media/blockchain-workbench-deploy/key-vault-app-create.png)

    | Impostazione  | Valore  |
    |---------|---------|
    | NOME | `Blockchain Key Vault app` |
    | Tipo di applicazione | App Web/API |
    | URL di accesso | `https://keyvaultclient |

5. Selezionare **Crea** per registrare l'applicazione Key Vault di Azure AD.

### <a name="get-tenant-domain-name"></a>Ottenere il nome di dominio del tenant

Raccogliere e archiviare il nome di dominio del tenant di Active Directory in cui vengono registrate le applicazioni. 

Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**. Selezionare **Nomi di dominio personalizzati**. Copiare e archiviare il nome di dominio.

![Nome di dominio](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Distribuire Blockchain Workbench

Dopo avere completato i passaggi preliminari necessari, è possibile distribuire Blockchain Workbench. Le sezioni seguenti descrivono come distribuire il framework.

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Selezionare l'account nell'angolo superiore destro e passare al tenant di Azure AD in cui si vuole distribuire Azure Blockchain Workbench.
3.  Nel riquadro a sinistra selezionare **Crea risorsa**. Cercare `Azure Blockchain Workbench` nella barra di ricerca **Cerca nel Marketplace**. 

    ![Barra di ricerca del Marketplace](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Selezionare **Azure Blockchain Workbench**.

    ![Risultati della ricerca nel Marketplace](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Selezionare **Create**.
5.  Completare le impostazioni di base.

    ![Creare Azure Blockchain Workbench](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Impostazione | DESCRIZIONE  |
    |---------|--------------|
    | Prefisso della risorsa | Identificatore univoco breve della distribuzione. Questo valore viene usato come base per la denominazione delle risorse. |
    | Nome utente macchina virtuale | Nome utente usato come amministratore per tutte le macchine virtuali (VM). |
    | Tipo di autenticazione | Selezionare questa opzione se si vuole usare una password o una chiave per la connessione alle macchine virtuali. |
    | Password | Password usata per la connessione alle macchine virtuali. |
    | SSH | Usare una chiave pubblica RSA in formato a una riga che inizi con **ssh-rsa** oppure usare il formato PEM a più righe. È possibile generare le chiavi SSH tramite `ssh-keygen` in Linux e OS X oppure usando PuTTYGen in Windows. Per altre informazioni sulle chiavi SSH, vedere [Come usare SSH con Windows in Azure](../virtual-machines/linux/ssh-from-windows.md). |
    | Database password (Password database) / Confirm database password (Conferma password database) | Specificare la password da usare per accedere al database creato nell'ambito della distribuzione. |
    | Sottoscrizione | Specificare la sottoscrizione di Azure che si vuole usare per la distribuzione. |
    | Gruppi di risorse | Creare un nuovo gruppo di risorse selezionando **Crea nuovo** e quindi specificare un nome di gruppo di risorse univoco. |
    | Località | Specificare l'area in cui si vuole distribuire il framework. |

6.  Selezionare **OK** per completare la sezione di configurazione delle impostazione di base.

7.  Completare la **configurazione di Azure Active Directory**.

    ![Configurazione di Azure AD](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Impostazione | DESCRIZIONE  |
    |---------|--------------|
    | Nome dominio | Usare il tenant di Azure AD raccolto nella sezione [Ottenere il nome di dominio del tenant](#get-tenant-domain-name) delle operazioni preliminari. |
    | ID applicazione | Usare l'ID applicazione della registrazione dell'app client Blockchain raccolto nella sezione [Ottenere l'ID applicazione](#get-application-id) delle operazioni preliminari. |
    | Chiave applicazione | Usare la chiave dell'applicazione della registrazione dell'app client Blockchain raccolta nella sezione [Aggiungere la chiave API Graph all'applicazione](#add-graph-api-key-to-application) delle operazioni preliminari. |


8.  Fare clic su **OK** per completare la sezione di configurazione dei parametri di Azure AD.

9.  Completare la sezione delle impostazioni **Network size and performance** (Dimensioni e prestazioni di rete).

    ![Impostazioni di rete e per le prestazioni](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Impostazione | DESCRIZIONE  |
    |---------|--------------|
    | Number of blockchain nodes (Numero di nodi blockchain) | Scegliere il numero di nodi di convalida Ethereum PoA da distribuire nella rete. |
    | Prestazioni dell'archiviazione | Scegliere le prestazioni di archiviazione preferite per le macchine virtuali per la rete blockchain. |
    | Dimensioni della macchina virtuale | Scegliere le dimensioni di macchina virtuale preferite per la rete blockchain. |

10. Fare clic su **OK** per completare la sezione relativa a dimensioni e prestazioni di rete.

11. Completare la sezione delle impostazioni **Monitoraggio di Azure**.

    ![Monitoraggio di Azure](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Impostazione | DESCRIZIONE  |
    |---------|--------------|
    | Monitoraggio | Scegliere se si vuole usare Monitoraggio di Azure per monitorare la rete blockchain. |
    | Connect to existing OMS instance (Connetti a istanza OMS esistente) | Scegliere se si vuole usare un'istanza di Operations Management Suite esistente o crearne una nuova. 

12. Fare clic su **OK** per completare la sezione Monitoraggio di Azure.

13. Esaminare il riepilogo per verificare che i parametri siano corretti.

    ![Summary](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Selezionare **Crea** per accettare le condizioni e distribuire Azure Blockchain Workbench.

La distribuzione può richiedere fino a 90 minuti. È possibile usare il portale di Azure per monitorare lo stato di avanzamento. Nel nuovo gruppo di risorse creato selezionare **Distribuzioni > Panoramica** per visualizzare lo stato degli elementi distribuiti.

## <a name="blockchain-workbench-web-url"></a>URL Web di Blockchain Workbench

Al termine della distribuzione di Blockchain Workbench, un nuovo gruppo di risorse contiene le risorse di Blockchain Workbench. È possibile accedere ai servizi Blockchain Workbench tramite un URL Web. I passaggi seguenti mostrano come recuperare l'URL Web del framework distribuito.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro di spostamento a sinistra selezionare **Gruppi di risorse**.
3. Scegliere il nome del gruppo di risorse specificato al momento della distribuzione di Blockchain Workbench.
4. Fare clic sull'intestazione di colonna **TIPO** per ordinare l'elenco in ordine alfabetico in base al tipo.
5. Sono presenti due risorse di tipo **Servizio app**. Selezionare la risorsa di tipo **Servizio app** *senza* il suffisso "-api".

    ![Elenco di servizi app](media/blockchain-workbench-deploy/resource-group-list.png)

6.  Nella sezione **Essentials** per il servizio app copiare il valore di **URL**, che rappresenta l'URL Web del framework Blockchain Workbench distribuito.

    ![Sezione Essentials del servizio app](media/blockchain-workbench-deploy/app-service.png)

## <a name="configuring-the-reply-url"></a>Configurazione dell'URL di risposta

Dopo la distribuzione di Azure Blockchain Workbench, il passaggio successivo consiste nell'assicurarsi che l'applicazione client Azure Active Directory (Azure AD) sia registrata per l'**URL di risposta** corretto per l'URL Web del framework Blockchain Workbench distribuito.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Verificare di trovarsi all'interno del tenant in cui è stata registrata l'applicazione client Azure AD.
3. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**. Selezionare **Registrazioni per l'app**.
4. Selezionare l'applicazione client Azure AD registrata nella sezione delle operazioni preliminari.
5. Selezionare **Impostazioni > URL di risposta**.
6. Specificare l'URL Web principale della distribuzione di Azure Blockchain Workbench recuperato nella sezione **Ottenere l'URL Web di Azure Blockchain Workbench**. L'URL di risposta è preceduto dal prefisso `https://`.  Ad esempio: `https://myblockchain2-7v75.azurewebsites.net`

    ![URL di risposta](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Selezionare **Salva** per aggiornare la registrazione del client.

## <a name="next-steps"></a>Passaggi successivi

Dopo che Azure Blockchain Workbench è stato distribuito, il passaggio successivo consiste nel [gestire gli utenti in Azure Blockchain Workbench](blockchain-workbench-manage-users.md).