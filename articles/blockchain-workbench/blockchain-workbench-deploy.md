---
title: Distribuire Azure Blockchain Workbench
description: Come distribuire Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e226aadbe499d5905b1814bec5d042f67d898c18
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294850"
---
# <a name="deploy-azure-blockchain-workbench"></a>Distribuire Azure Blockchain Workbench

Azure Blockchain Workbench viene distribuito usando un modello di soluzione in Azure Marketplace. Il modello semplifica la distribuzione dei componenti necessari per creare applicazioni blockchain. Una volta distribuito, Blockchain Workbench fornisce l'accesso alle app client per creare e gestire utenti e applicazioni blockchain.

Per altre informazioni sui componenti di Blockchain Workbench, vedere [Architettura di Azure Blockchain Workbench](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Blockchain Workbench consente di distribuire un libro mastro blockchain insieme a un set di servizi di Azure pertinenti usati frequentemente per compilare un'applicazione basata su blockchain. La distribuzione di Blockchain Workbench comporta il provisioning dei seguenti servizi Azure all'interno di un gruppo di risorse nella sottoscrizione Azure.

* 1 argomento di Griglia di eventi
* 1 spazio dei nomi del bus di servizio
* 1 Application Insights
* 1 database SQL (S0 Standard)
* 2 Servizi app (Standard)
* 2 Azure Key Vault
* 2 account di Archiviazione di Azure (archiviazione con ridondanza locale standard)
* 2 set di scalabilità di macchine virtuali (per i nodi di convalida e di lavoro)
* 2 reti virtuali (che includono il bilanciamento del carico, il gruppo di sicurezza di rete e l'indirizzo IP pubblico per ogni rete virtuale)
* Facoltativo: Monitoraggio di Azure

Di seguito è riportato un esempio di distribuzione creata nel gruppo di risorse **myblockchain**.

![Distribuzione di esempio](media/blockchain-workbench-deploy/example-deployment.png)

Il costo di Blockchain Workbench è un'aggregazione del costo dei servizi Azure sottostanti. Per calcolare i prezzi per i servizi di Azure, usare il [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench richiede l'esecuzione di diverse operazioni preliminari prima della distribuzione. Queste operazioni preliminari includono le registrazioni dell'applicazione e della configurazione di Azure AD.

### <a name="blockchain-workbench-api-app-registration"></a>Registrazione dell'app per le API per Blockchain Workbench

Per la distribuzione di Blockchain Workbench, è necessaria la registrazione di un'applicazione Azure AD. Per registrare l'app, è necessario un tenant di Azure Active Directory (Azure AD). È possibile usare un tenant esistente oppure crearne uno nuovo. Se si usa un tenant di Azure AD esistente, sono necessarie autorizzazioni sufficienti per registrare le applicazioni e concedere le autorizzazioni per l'API Graph all'interno di un tenant di Azure AD. Se non sono disponibili autorizzazioni sufficienti in un tenant di Azure AD esistente, creare un nuovo tenant. 

> [!IMPORTANT]
> Non è necessario distribuire Workbench nello stesso tenant di quello usato per registrare un'applicazione Azure AD. Workbench deve essere distribuito in un tenant in cui sono disponibili autorizzazioni sufficienti per distribuire le risorse. Per altre informazioni sui tenant di Azure AD, vedere [Come ottenere un tenant di Active Directory](../active-directory/develop/active-directory-howto-tenant.md) e [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

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
2. Generare un GUID. È possibile generare un GUID usando il comando di PowerShell [guid] :: NewGuid () o il cmdlet New-GUID. Un'altra opzione consiste nell'usare un sito Web di generatori di GUID.
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

### <a name="get-application-id"></a>Ottenere l'ID applicazione

Le informazioni su ID e tenant dell'applicazione sono necessarie per la distribuzione. Raccogliere e archiviare le informazioni per usarle durante la distribuzione.

1. Per l'applicazione registrata, selezionare **Impostazioni** > **Proprietà**.
2.  Nel riquadro **Proprietà** copiare e archiviare i valori seguenti per usarli durante la distribuzione.

    ![Proprietà dell'app per le API](media/blockchain-workbench-deploy/app-properties.png)

    | Impostazione da archiviare  | Da usare nella distribuzione |
    |------------------|-------------------|
    | ID applicazione | Azure Active Directory setup (Configurazione di Azure Active Directory) > ID applicazione |

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
    | Area di distribuzione | Specificare dove distribuire le risorse di Blockchain Workbench. Per ottimizzare la disponibilità, questa impostazione deve corrispondere alla **Località**. |
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
    | Monitoraggio | Scegliere se si vuole abilitare Monitoraggio di Azure per il monitoraggio della rete blockchain. |
    | Connect to existing Log Analytics instance (Connetti all'istanza di Log Analytics esistente) | Scegliere se si vuole usare un'istanza di Log Analytics esistente o crearne una nuova. Se si usa un'istanza esistente, immettere l'ID dell'area di lavoro e la chiave primaria. |

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

Per associare un nome di dominio personalizzato a Blockchain Workbench, vedere [Configurazione di un nome di dominio personalizzato per un'app Web nel servizio app di Azure con Gestione traffico](../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Configurazione dell'URL di risposta

Dopo la distribuzione di Azure Blockchain Workbench, il passaggio successivo consiste nell'assicurarsi che l'applicazione client Azure Active Directory (Azure AD) sia registrata per l'**URL di risposta** corretto per l'URL Web del framework Blockchain Workbench distribuito.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Verificare di trovarsi all'interno del tenant in cui è stata registrata l'applicazione client Azure AD.
3. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**. Selezionare **Registrazioni per l'app**.
4. Selezionare l'applicazione client Azure AD registrata nella sezione delle operazioni preliminari.
5. Selezionare **Impostazioni > URL di risposta**.
6. Specificare l'URL Web principale della distribuzione di Azure Blockchain Workbench recuperato nella sezione **Ottenere l'URL Web di Azure Blockchain Workbench**. L'URL di risposta è preceduto dal prefisso `https://`. Ad esempio: `https://myblockchain2-7v75.azurewebsites.net`

    ![URL di risposta](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Selezionare **Salva** per aggiornare la registrazione del client.

## <a name="remove-a-deployment"></a>Rimuovere una distribuzione

È possibile rimuovere una distribuzione non più necessaria eliminando il gruppo di risorse di Blockchain Workbench.

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare. 
2. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

    ![Eliminare un gruppo di risorse](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>Passaggi successivi

In questa procedura dettagliata si è eseguita la distribuzione di Azure Blockchain Workbench. Per informazioni su come creare un'applicazione blockchain, proseguire con la procedura dettagliata successiva.

> [!div class="nextstepaction"]
> [Creare un'applicazione blockchain in Azure Blockchain Workbench](blockchain-workbench-create-app.md)
