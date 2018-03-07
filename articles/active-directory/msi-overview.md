---
title: "Identità del servizio gestito per Azure Active Directory"
description: "Panoramica dell'Identità del servizio gestito per le risorse di Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: 72e1d5250de214c063a970f5761105b2329ccf7f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identità del servizio gestito per le risorse di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione delle credenziali che devono essere presenti nel codice per l'autenticazione ai servizi cloud. Proteggere le credenziali è un'attività importante. In teoria, non sono mai presenti nelle workstation di sviluppo oppure vengono verificate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. Identità del servizio gestito consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory, ovvero Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

## <a name="how-does-it-work"></a>Come funziona?

Quando si abilita Identità del servizio gestito in un servizio di Azure, Azure crea automaticamente un'identità per l'istanza del servizio nel tenant di Azure AD usato dalla sottoscrizione di Azure.  A livello sottostante, Azure offre le credenziali per l'identità nell'istanza del servizio.  Il codice può quindi fare una richiesta locale per ottenere i token di accesso ai servizi che supportano l'autenticazione di Azure AD.  Azure gestisce le credenziali usate dall'istanza del servizio in sequenza.  Se l'istanza del servizio viene eliminata, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.

Di seguito è riportato un esempio di come Identità del servizio gestito funziona con le macchine virtuali di Azure.

![Esempio di Identità del servizio gestito per la macchina virtuale](./media/msi-vm-example.png)

1. Azure Resource Manager riceve un messaggio per abilitare l'Identità del servizio gestito in una macchina virtuale.
2. Azure Resource Manager crea un'entità servizio in Azure AD per rappresentare l'identità della macchina virtuale. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile da questa sottoscrizione.
3. Azure Resource Manager consente di configurare i dettagli dell'entità servizio nell'estensione dell'Identità del servizio gestito della macchina virtuale.  Questo passaggio include la configurazione dell'ID client e del certificato usato dall'estensione per ottenere i token di accesso da Azure AD.
4. Ora che l'identità dell'entità servizio della macchina virtuale è nota, è possibile concedere l'accesso alle risorse di Azure.  Ad esempio, se il codice deve chiamare Azure Resource Manager, è necessario assegnare all'entità servizio della macchina virtuale il ruolo appropriato usando il controllo degli accessi in base ai ruoli in Azure AD.  Se il codice deve chiamare Key Vault, è necessario concedere al codice l'accesso alla chiave o al segreto specifico in Key Vault.
5. Il codice in esecuzione nella macchina virtuale richiede un token da un endpoint locale ospitato dall'estensione della macchina virtuale per l'Identità del servizio gestito: http://localhost:50342/oauth2/token.  Il parametro della risorsa specifica il servizio a cui viene inviato il token. Ad esempio, se si desidera che il codice venga autenticato in Azure Resource Manager, si userà resource=https://management.azure.com/.
6. L'estensione della macchina virtuale per l'Identità del servizio gestito usa l'ID client configurato e un certificato per richiedere un token di accesso da Azure AD.  Azure AD restituisce un token di accesso JSON Web.
7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.

Ogni servizio di Azure che supporta l'Identità del servizio gestito ha il proprio metodo per ottenere il token di accesso nel codice. Consultare le esercitazioni per ogni servizio per vedere il metodo specifico per ottenere un token.

## <a name="try-managed-service-identity"></a>Provare l'Identità del servizio gestito

Eseguire un'esercitazione relativa a Identità del servizio gestito per informazioni sugli scenari end-to-end per l'accesso alle diverse risorse di Azure:
<br><br>
| Da risorse basate su Identità del servizio gestito | Scopri come |
| ------- | -------- |
| Macchina virtuale Azure (Windows) | [Usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Azure Data Lake Store](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Usare un'Identità del servizio gestito per una macchina virtuale Windows per accedere ad Azure Resource Manager](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Accedere ad Azure SQL con un'identità del servizio gestito di una macchina virtuale Windows](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Accedere ad Archiviazione di Azure tramite chiave di accesso con un'identità del servizio gestito di una macchina virtuale Windows](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Accedere ad Archiviazione di Azure tramite firma di accesso condiviso con un'identità del servizio gestito di una macchina virtuale Windows](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Usare un'Identità del servizio gestito per una macchina virtuale Windows e Azure Key Vault per accedere a una risorsa diversa da Azure AD](msi-tutorial-windows-vm-access-nonaad.md) |
| Macchina virtuale Azure (Linux)   | [Usare un'identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Data Lake Store](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Usare un'Identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Resource Manager](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Accedere ad Archiviazione di Azure tramite chiave di accesso con un'identità del servizio gestito di una macchina virtuale Linux](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Accedere ad Archiviazione di Azure tramite firma di accesso condiviso con un'identità del servizio gestito di una macchina virtuale Linux](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Accedere a una risorsa non Azure AD con l'identità del servizio gestito di una macchina virtuale Linux e con Azure Key Vault](msi-tutorial-linux-vm-access-nonaad.md) |
| Servizio app di Azure  | [Usare Identità del servizio gestito con Funzioni di Azure o Servizio app di Azure](/azure/app-service/app-service-managed-service-identity) |
| Funzione di Azure     | [Usare Identità del servizio gestito con Funzioni di Azure o Servizio app di Azure](/azure/app-service/app-service-managed-service-identity) |
| Bus di servizio di Azure  | [Usare l'identità del servizio gestito con il bus di servizio di Azure](../service-bus-messaging/service-bus-managed-service-identity.md) |
| Hub eventi di Azure   | [Usare l'identità del servizio gestito con Hub eventi di Azure](../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Quali servizi di Azure supportano l'Identità del servizio gestito?

I servizi di Azure che supportano l'Identità del servizio gestito possono usare l'Identità del servizio gestito per eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD.  È in corso l'integrazione dell'autenticazione di Azure AD e dell'Identità del servizio gestito in Azure.  Controllare spesso gli aggiornamenti.

### <a name="azure-services-that-support-managed-service-identity"></a>Servizi di Azure che supportano Identità del servizio gestito

I servizi di Azure seguenti supportano Identità del servizio gestito.

| Service | Status | Data | Configurare | Acquisizione di un token |
| ------- | ------ | ---- | --------- | ----------- |
| Macchine virtuali di Azure | Preview | Settembre 2017 | [Azure portal](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[Interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md)<br>[Modelli di Gestione risorse di Azure](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Go](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Servizio app di Azure | Preview | Settembre 2017 | [Azure portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Funzioni di Azure | Preview | Settembre 2017 | [Azure portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modello di Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Preview | Novembre 2017 | [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano l'Identità del servizio gestito.

| Service | ID risorsa | Status | Data | Assegnare l'accesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Gestione risorse di Azure | https://management.azure.com | Disponibile | Settembre 2017 | [Azure portal](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[Interfaccia della riga di comando di Azure](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Disponibile | Settembre 2017 | |
| Azure Data Lake | https://datalake.azure.net | Disponibile | Settembre 2017 | |
| SQL di Azure | https://database.windows.net | Disponibile | Ottobre 2017 | |
| Hub eventi di Azure | https://eventhubs.azure.net | Disponibile | Dicembre 2017 | |
| Bus di servizio di Azure | https://servicebus.azure.net | Disponibile | Dicembre 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Quanto costa Identità del servizio gestito?

Identità del servizio gestito viene fornito con Azure Active Directory Free, ovvero il servizio predefinito per le sottoscrizioni di Azure.  Non ci sono costi aggiuntivi per l'Identità del servizio gestito.

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

I commenti degli utenti sono molto apprezzati.

* È possibile inserire domande sulle procedure su Stack Overflow con il tag [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* È possibile fare richieste sulla funzionalità o inviare un commento sul [forum per i commenti e i suggerimenti di Azure AD per gli sviluppatori](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






