---
title: Le decisioni di distribuzione connessi di Azure per lo Stack di Azure integrati sistemi | Documenti Microsoft
description: "Determinare le decisioni per le distribuzioni di Azure Stack Azure connesse a più nodi di pianificazione della distribuzione."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: def9d5381144026b5ad0e8a076edd3c0692a08f4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Distribuzione di Azure connessa pianificazione decisioni per lo Stack di Azure integrati sistemi
Dopo aver deciso [integrerà come Stack di Azure nell'ambiente cloud ibrido](azure-stack-connection-models.md), quindi è possibile finalizzare le decisioni relative alla distribuzione di Azure Stack.

Distribuzione dello Stack di Azure connesso ad Azure significa che è possibile Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) per l'archivio di identità. È anche possibile scegliere da un modello di fatturazione: retribuzione come-si- o uso basati sulla capacità. Una distribuzione connessa è l'opzione predefinita perché consente ai clienti di ottenere il massimo valore fuori dello Stack di Azure, in particolare per scenari di cloud ibrido che coinvolgono Azure e Azure Stack. 

## <a name="choose-an-identity-store"></a>Scegliere un archivio di identità
Con una distribuzione connessa, è possibile scegliere tra Azure Active Directory o ADFS per l'archivio di identità. Una distribuzione disconnessa, senza connessione internet, può utilizzare solo AD FS.

La scelta di archivio identità non è rilevante per macchine virtuali tenant (VM). Macchine virtuali tenant possono scegliere il negozio di identità desiderato per la connessione a, a seconda di come verranno configurati: Azure AD, gruppo di lavoro appartenenti a un dominio Windows Server Active Directory e così via. Questo non è correlato alla decisione di provider di identità dello Stack di Azure. 

Ad esempio, se si distribuire macchine virtuali all'inizio dello Stack di Azure tenant di IaaS e desidera che possano partecipare a un dominio Active Directory aziendale e utilizzare gli account da qui, è possibile comunque farlo. Non è necessario usare l'archivio di identità di Azure AD selezionato qui per tali account.

### <a name="azure-ad-identity-store"></a>Archivio identità di Azure AD
Quando si usa Azure AD per l'archivio di identità richiede due account di Azure AD: un account amministratore globale e un account di fatturazione. Questi account possono essere l'account stesso, o account diversi. Utilizzando lo stesso account utente potrebbe essere più semplice e utile se si dispone di un numero limitato di account di Azure, le esigenze aziendali potrebbero suggerire utilizzando due account:

1. **Account di amministratore globale** (necessaria solo per le distribuzioni connesse). Si tratta di un account di Azure che viene utilizzato per creare applicazioni e delle entità servizio per servizi di infrastruttura di Azure Stack in Azure Active Directory. Questo account deve disporre di autorizzazioni di amministratore di directory alla directory in cui verrà distribuito il sistema Azure Stack in. Quest ' ultimo è l'operatore"cloud" amministratore globale per Azure Active Directory del tenant e verrà utilizzato: 
    - Per eseguire il provisioning e il delegato applicazioni ed entità di servizio per tutti i servizi di Azure Stack che devono interagire con Azure Active Directory e l'API Graph. 
    - Come l'account di amministratore del servizio. Questo è il proprietario della sottoscrizione del provider predefinito (che è possibile modificare successivamente). Si può accedere al portale di amministrazione di Azure Stack con questo account e usarlo per creare offerte e i piani e impostare le quote di eseguire altre funzioni amministrative nello Stack di Azure.
2. **Account di fatturazione** (obbligatorio per entrambi collegate e scollegate distribuzioni). Questo account di Azure viene utilizzato per stabilire la relazione tra il sistema integrato dello Stack di Azure e di back-end di Azure commerce fatturazione. Si tratta dell'account che verrà fatturato per gli addebiti di Stack di Azure. Questo account verrà utilizzato anche per la diffusione di marketplace e altri scenari ibridi. 

### <a name="ad-fs-identity-store"></a>Archivio di identità AD FS
Scegliere questa opzione se si desidera utilizzare un archivio di identità, ad esempio Active Directory aziendale, per gli account di amministratore del servizio.  

## <a name="choose-a-billing-model"></a>Scegliere un modello di fatturazione
È possibile scegliere una **retribuzione come-di-utilizzo** o **capacità** modello di fatturazione. Le distribuzioni modello fatturazione retribuzione come-di-uso devono essere in grado di utilizzo di report tramite una connessione a Azure almeno una volta ogni 30 giorni. Pertanto, il modello di fatturazione retribuzione come-di-uso è disponibile solo per le distribuzioni connesse.  

### <a name="pay-as-you-use"></a>Retribuzione come-di-utilizzo
Con il modello di fatturazione, pagamento come-di-utilizzo viene addebitato sull'utilizzo di una sottoscrizione di Azure. Si paga solo quando si utilizzano i servizi di Azure Stack. Se si tratta del modello su cui si decide, occorre una sottoscrizione di Azure e l'ID account associate alla sottoscrizione (ad esempio, serviceadmin@contoso.onmicrosoft.com). EA CSP e CSL sottoscrizioni sono supportate. Report sull'utilizzo viene configurato durante [registrazione Azure Stack](azure-stack-registration.md).

> [!NOTE]
> Nella maggior parte dei casi, i clienti aziendali utilizzerà le sottoscrizioni EA e provider di servizi userà CSP o CSL sottoscrizioni.

Se si intende utilizzare una sottoscrizione di CSP, esaminare la tabella seguente per identificare la sottoscrizione di CSP da utilizzare, come l'approccio corretto varia a seconda dello scenario CSP:

|Scenario|Opzioni di sottoscrizione e di dominio|
|-----|-----|
|Si è un **Partner CSP diretto** o **indiretta Provider CSP**, e si opererà lo Stack di Azure|Utilizzare una sottoscrizione CSL (livello di servizio comune).<br>     oppure<br>Creare un tenant di Azure AD con un nome descrittivo in Partner Center. Ad esempio &lt;organizzazione > CSPAdmin con una sottoscrizione di Azure CSP associata.|
|Si è un **indiretta CSP Reseller**, e si opererà lo Stack di Azure|Chiedere al Provider CSP indiretto per creare un tenant di Azure AD dell'organizzazione con una sottoscrizione di Azure CSP associata utilizzando il centro di Partner.|

### <a name="capacity-based-billing"></a>Capacità di fatturazione in base
Se si decide di utilizzare il modello di fatturazione di capacità, è necessario acquistare un Azure Stack capacità pianificare SKU in base alla capacità del sistema. È necessario conoscere il numero di core fisici in pila Azure acquistare la quantità corretta. 

Capacità fatturazione richiede un Enterprise Agreement (EA) per la registrazione della sottoscrizione di Azure. Il motivo è che i set di registrazione di diffusione, che richiede una sottoscrizione di Azure. La sottoscrizione non viene usata per l'utilizzo dello Stack di Azure.

## <a name="learn-more"></a>Altre informazioni
- Per informazioni sui casi di utilizzo, acquisto, partner e fornitori di hardware OEM, vedere il [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) pagina del prodotto.
- Per informazioni sulla Guida di orientamento e geografica disponibilità per lo Stack di Azure sistemi integrati, vedere il white paper: [dello Stack di Azure: un'estensione di Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Per ulteriori informazioni su Microsoft Azure Stack assemblaggio e prezzi [scaricare il PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Passaggi successivi
[Integrazione della rete datacenter](azure-stack-network.md)