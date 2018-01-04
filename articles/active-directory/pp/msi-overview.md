---
title: "Identità del servizio gestito per Azure Active Directory"
description: "Panoramica dell'Identità del servizio gestito per le risorse di Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 53577c8da5f82235284d1cb9e48f2d47254aa6bd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identità del servizio gestito per le risorse di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione delle credenziali che devono essere presenti nel codice per l'autenticazione ai servizi cloud. Proteggere le credenziali è un'attività importante. In teoria, non sono mai presenti nelle workstation di sviluppo oppure vengono verificate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. Identità del servizio gestito consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory, ovvero Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

## <a name="how-does-it-work"></a>Come funziona?

Quando si utilizza un'identità del servizio gestito in un'istanza del servizio di Azure, Azure crea un'identità nel tenant di Azure AD utilizzato dalla sottoscrizione di Azure. Inoltre, Azure fornisce le credenziali per l'identità, sull'istanza del servizio. Di conseguenza, il codice può quindi rendere una richiesta per ottenere i token di accesso per i servizi che supportano l'autenticazione di Azure Active Directory locale. Mentre Azure gestisce le credenziali utilizzate dall'istanza del servizio in sequenza.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Come abilitare le risorse da utilizzare un'identità del servizio gestito?

Sono disponibili due tipi di identità del servizio gestiti: *assegnato dal sistema* e *utente assegnato*.

- Oggetto **assegnato dal sistema** MSI è abilitato direttamente in un'istanza del servizio di Azure. Tramite il processo di abilitazione, Azure crea un'identità per l'istanza del servizio nel tenant di Azure AD e le credenziali per l'identità nell'istanza del servizio viene eseguito il provisioning. Il ciclo di vita di un sistema MSI assegnato è direttamente collegato a Azure l'istanza del servizio è abilitato nel. Se l'istanza del servizio viene eliminata, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.

- Oggetto **utente assegnato** MSI *(anteprima privata)* viene creata come una risorsa di Azure autonoma. Tramite il processo di creazione, Azure crea un'identità nel tenant di Azure AD. Dopo aver creato l'identità, può essere assegnato a una o più istanze di servizio di Azure. Poiché un utente assegnato MSI può essere utilizzato da più istanze del servizio di Azure, il ciclo di vita viene gestito separatamente.

Di seguito è riportato un esempio di come un file MSI assegnato dal sistema funziona con macchine virtuali di Azure.

![Esempio di Identità del servizio gestito per la macchina virtuale](~/articles/active-directory/media/msi-vm-example.png)

1. Gestione risorse di Azure riceve un messaggio per abilitare il file MSI assegnato dal sistema in una macchina virtuale.
2. Azure Resource Manager crea un'entità servizio in Azure AD per rappresentare l'identità della macchina virtuale. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile da questa sottoscrizione.
3. Azure Resource Manager consente di configurare i dettagli dell'entità servizio nell'estensione dell'Identità del servizio gestito della macchina virtuale. Questo passaggio include la configurazione dell'ID client e del certificato usato dall'estensione per ottenere i token di accesso da Azure AD.
4. Ora che l'identità dell'entità servizio della macchina virtuale è nota, è possibile concedere l'accesso alle risorse di Azure. Ad esempio, se il codice deve chiamare Azure Resource Manager, è necessario assegnare all'entità servizio della macchina virtuale il ruolo appropriato usando il controllo degli accessi in base ai ruoli in Azure AD.  Se il codice deve chiamare Key Vault, è necessario concedere al codice l'accesso alla chiave o al segreto specifico in Key Vault.
5. Il codice in esecuzione nella macchina virtuale richiede un token da un endpoint locale ospitato dall'estensione MSI VM: oauth2/http://localhost:50342/token. Il parametro della risorsa specifica il servizio a cui viene inviato il token. Ad esempio, se si desidera che il codice venga autenticato in Azure Resource Manager, si userà resource=https://management.azure.com/.
6. L'estensione della macchina virtuale per l'Identità del servizio gestito usa l'ID client configurato e un certificato per richiedere un token di accesso da Azure AD.  Azure AD restituisce un token di accesso JSON Web.
7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.

Utilizzando stesso, questo punto del diagramma di un esempio del funzionamento di un file MSI assegnati dall'utente con macchine virtuali di Azure.

![Esempio di Identità del servizio gestito per la macchina virtuale](~/articles/active-directory/media/msi-vm-example.png)

1. Gestione risorse di Azure riceve un messaggio per creare un file MSI assegnati dall'utente.
2. Gestione risorse di Azure crea un'entità servizio in Azure AD per rappresentare l'identità del file MSI. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile da questa sottoscrizione.
3. Gestione risorse di Azure riceve un messaggio per configurare i dettagli dell'entità servizio nell'estensione MSI VM di una macchina virtuale. Questo passaggio include la configurazione dell'ID client e del certificato usato dall'estensione per ottenere i token di accesso da Azure AD.
4. Ora che l'identità dell'entità servizio del file MSI è nota, è possibile concesso l'accesso alle risorse di Azure. Ad esempio, se il codice deve chiamare Azure Resource Manager, quindi è necessario assegnare dell'entità servizio del file MSI il ruolo appropriato con controllo di accesso basato sui ruoli (RBAC) in Azure AD. Se il codice deve chiamare Key Vault, è necessario concedere al codice l'accesso alla chiave o al segreto specifico in Key Vault. Nota: Passaggio 3 non è necessario per completare il passaggio 4. Una volta creato un file MSI, può essere concesso l'accesso alle risorse, indipendentemente dal fatto o non viene configurato in una macchina virtuale.
5. Il codice in esecuzione nella macchina virtuale richiede un token da un endpoint locale ospitato dall'estensione della macchina virtuale per l'Identità del servizio gestito: http://localhost:50342/oauth2/token. Il parametro ID client specifica il nome dell'identità del file MSI da utilizzare. Il parametro resource specifica inoltre il servizio a cui il token viene inviato. Ad esempio, se si desidera che il codice venga autenticato in Azure Resource Manager, si userà resource=https://management.azure.com/.
6. L'estensione VM MSI controlla se il certificato per l'ID richiesta client è configurato e richiede un token di accesso da Azure AD. Azure AD restituisce un token di accesso JSON Web.
7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.

Ogni servizio di Azure che supporta l'Identità del servizio gestito ha il proprio metodo per ottenere il token di accesso nel codice. Consultare le esercitazioni per ogni servizio per vedere il metodo specifico per ottenere un token.

## <a name="try-managed-service-identity"></a>Provare l'Identità del servizio gestito

Eseguire un'esercitazione relativa a Identità del servizio gestito per informazioni sugli scenari end-to-end per l'accesso alle diverse risorse di Azure:
<br><br>
| Da risorse basate su Identità del servizio gestito | Scopri come |
| ------- | -------- |
| Macchina virtuale Azure (Linux)   | [Usare un'Identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Resource Manager](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Accedere ad Archiviazione di Azure tramite chiave di accesso con un'identità del servizio gestito di una macchina virtuale Linux](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Quali servizi di Azure supportano l'Identità del servizio gestito?

I servizi di Azure che supportano l'Identità del servizio gestito possono usare l'Identità del servizio gestito per eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD.  È in corso l'integrazione dell'autenticazione di Azure AD e dell'Identità del servizio gestito in Azure.  Controllare spesso gli aggiornamenti.

### <a name="azure-services-that-support-managed-service-identity"></a>Servizi di Azure che supportano Identità del servizio gestito

I servizi di Azure seguenti supportano Identità del servizio gestito.

| Service | Status | Data | Configurare | Acquisizione di un token |
| ------- | ------ | ---- | --------- | ----------- |
| Macchine virtuali di Azure | Preview | Settembre 2017 | [Interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md)<br>[Modelli di Gestione risorse di Azure](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano l'Identità del servizio gestito.

| Service | ID risorsa | Status | Data | Assegnare l'accesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Gestione risorse di Azure | https://management.azure.com/ | Disponibile | Settembre 2017 | [Interfaccia della riga di comando di Azure](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Disponibile | Settembre 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponibile | Settembre 2017 | |
| SQL di Azure | https://database.windows.net/ | Disponibile | Ottobre 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Quanto costa Identità del servizio gestito?

Identità del servizio gestito viene fornito con Azure Active Directory Free, ovvero il servizio predefinito per le sottoscrizioni di Azure.  Non ci sono costi aggiuntivi per l'Identità del servizio gestito.

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

I commenti degli utenti sono molto apprezzati.

* È possibile inserire domande sulle procedure su Stack Overflow con il tag [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* È possibile fare richieste sulla funzionalità o inviare un commento sul [forum per i commenti e i suggerimenti di Azure AD per gli sviluppatori](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






