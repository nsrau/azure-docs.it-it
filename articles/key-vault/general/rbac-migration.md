---
title: Eseguire la migrazione al controllo degli accessi in base al ruolo di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione dai criteri di accesso all'insieme di credenziali ai ruoli di Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 23a36bfc048a6214ccb79b793a23c21d5f8e305e
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288264"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>Eseguire la migrazione dai criteri di accesso all'insieme di credenziali a un modello di autorizzazione di controllo degli accessi in base al ruolo di Azure

Il modello di criteri di accesso all'insieme di credenziali è un sistema di autorizzazione esistente incorporato Key Vault per fornire accesso a chiavi, segreti e certificati. È possibile controllare l'accesso assegnando singole autorizzazioni all'entità di sicurezza (utente, gruppo, entità servizio, identità gestita) nell'ambito Key Vault. 

Il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) è un sistema di autorizzazione basato su [Azure Resource Manager](../../azure-resource-manager/management/overview.md) che offre una gestione degli accessi con granularità fine delle risorse di Azure. Il controllo degli accessi in base al ruolo di Azure per le chiavi Key Vault, i segreti e i certificati è attualmente in anteprima pubblica. Con il controllo degli accessi in base al ruolo di Azure è possibile controllare l'accesso alle risorse creando assegnazioni di ruoli, costituite da tre elementi: entità di sicurezza, definizione del ruolo (set predefinito di autorizzazioni) e ambito (gruppo di risorse o singola risorsa). Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md).

Prima di eseguire la migrazione a RBAC di Azure, è importante comprenderne i vantaggi e le limitazioni.

Vantaggi principali di Azure RBAC sui criteri di accesso dell'insieme di credenziali:
- Fornisce il modello di controllo di accesso unificato per le risorse di Azure, ovvero la stessa API nei servizi di Azure
- Gestione centralizzata degli accessi per gli amministratori-gestione di tutte le risorse di Azure in un'unica visualizzazione
- Integrato con [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) per il controllo degli accessi in base al tempo
- Nega assegnazioni: possibilità di escludere un'entità di sicurezza in un ambito specifico. Per informazioni, vedere informazioni sulle [assegnazioni di negamento di Azure](../../role-based-access-control/deny-assignments.md)

Svantaggi RBAC di Azure:
- Latenza per le assegnazioni di ruolo. possono essere necessari alcuni minuti per l'applicazione dell'assegnazione di ruolo. I criteri di accesso dell'insieme di credenziali vengono assegnati immediatamente.
- Numero limitato di assegnazioni di ruolo-2000 ruoli assegnati per sottoscrizione rispetto a 1024 criteri di accesso per Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Criteri di accesso al mapping dei ruoli di Azure

Il controllo degli accessi in base al ruolo di Azure include diversi ruoli predefiniti di Azure che è possibile assegnare a utenti, gruppi, entità servizio e identità gestite. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md).

Key Vault ruoli predefiniti per le chiavi, i certificati e la gestione dell'accesso ai segreti:
- Amministratore Key Vault (anteprima)
- Lettore di Key Vault (anteprima)
- Officer certificate Key Vault (anteprima)
- Key Vault Crypto Officer (anteprima)
- Utente di crittografia Key Vault (anteprima)
- Amministratore di Key Vault Secrets (anteprima)
- Utente di Key Vault Secrets (anteprima)

Per ulteriori informazioni sui ruoli predefiniti esistenti, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md) .

I criteri di accesso all'insieme di credenziali possono essere assegnati con autorizzazioni selezionate singolarmente o con modelli di autorizzazione predefiniti.

Criteri di accesso modelli di autorizzazione predefiniti:
- Chiave, segreto, gestione certificati
- Chiave & gestione dei segreti
- Gestione dei certificati & segreti
- Gestione chiavi
- Gestione dei segreti
- Gestione dei certificati
- Connettore SQL Server
- Azure Data Lake Storage o archiviazione di Azure
- Backup di Azure
- Chiave cliente di Exchange Online
- Chiave cliente di SharePoint Online
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Accedere ai modelli di criteri al mapping dei ruoli di Azure
| Modello di criteri di accesso | Operazioni | Ruolo di Azure |
| --- | --- | --- |
| Chiave, segreto, gestione certificati | Chiavi: tutte le operazioni <br>Certificati: tutte le operazioni<br>Segreti: tutte le operazioni | Amministratore Key Vault (anteprima) |
| Chiave & gestione dei segreti | Chiavi: tutte le operazioni <br>Segreti: tutte le operazioni| Key Vault Crypto Officer (anteprima)<br> Amministratore di Key Vault Secrets (anteprima)|
| Gestione dei certificati & segreti | Certificati: tutte le operazioni <br>Segreti: tutte le operazioni| Ufficiale certificati Key Vault (anteprima)<br> Amministratore di Key Vault Secrets (anteprima)|
| Gestione chiavi | Chiavi: tutte le operazioni| Key Vault Crypto Officer (anteprima)|
| Gestione dei segreti | Segreti: tutte le operazioni| Amministratore di Key Vault Secrets (anteprima)|
| Gestione dei certificati | Certificati: tutte le operazioni | Ufficiale certificati Key Vault (anteprima)|
| Connettore SQL Server | Chiavi: Get, List, wrap Key, Unwrap Key | Crittografia del servizio di crittografia Key Vault (anteprima)|
| Azure Data Lake Storage o archiviazione di Azure | Chiavi: ottenere, elencare, annullare il wrapping della chiave | N/D<br> Il ruolo personalizzato è obbligatorio|
| Backup di Azure | Chiavi: Get, List, backup<br> Certificato: Get, List, backup | N/D<br> Il ruolo personalizzato è obbligatorio|
| Chiave cliente di Exchange Online | Chiavi: Get, List, wrap Key, Unwrap Key | Crittografia del servizio di crittografia Key Vault (anteprima)|
| Chiave cliente di Exchange Online | Chiavi: Get, List, wrap Key, Unwrap Key | Crittografia del servizio di crittografia Key Vault (anteprima)|
| Azure Information BYOK | Chiavi: Get, Decrypt, Sign | N/D<br>Il ruolo personalizzato è obbligatorio|


## <a name="assignment-scopes-mapping"></a>Mapping degli ambiti di assegnazione  

Il controllo degli accessi in base al ruolo di Azure per la Key Vault consente l'assegnazione di ruoli
- Gruppo di gestione
- Subscription
- Resource group
- Risorsa Key Vault
- Chiave singola, segreto e certificato

Il modello di autorizzazione dei criteri di accesso all'insieme di credenziali è limitato all'assegnazione dei criteri solo a livello di Key Vault risorse, che 

In generale, è consigliabile avere un insieme di credenziali delle chiavi per ogni applicazione e gestire l'accesso a livello di Key Vault. Esistono scenari in cui la gestione dell'accesso in altri ambiti può semplificare la gestione degli accessi.

- * * Infrastruttura, amministratori della sicurezza e operatori: la gestione di un gruppo di insiemi di credenziali delle chiavi a livello di gruppo di gestione, di sottoscrizione o di gruppo di risorse con i criteri di accesso dell'insieme di credenziali richiede la gestione dei criteri Il controllo degli accessi in base al ruolo di Azure consente di creare un'assegnazione di ruolo a livello di gruppo di gestione, sottoscrizione Tale assegnazione viene applicata a tutti i nuovi insiemi di credenziali delle chiavi creati nello stesso ambito. In questo scenario è consigliabile usare Privileged Identity Management con accesso JIT per fornire l'accesso permanente.
 
- * * Applicazioni: esistono scenari in cui l'applicazione deve condividere il segreto con altre applicazioni. Per evitare di concedere l'accesso a tutti i segreti, è necessario creare criteri di accesso all'insieme di credenziali. Il controllo degli accessi in base al ruolo di Azure consente di assegnare un ruolo con ambito per il singolo segreto, usando

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Criteri di accesso dell'insieme di credenziali ai passaggi della migrazione di Azure RBAC
Esistono molte differenze tra il controllo degli accessi in base al ruolo e il modello di autorizzazione dei criteri di accesso Per evitare interruzioni durante la migrazione, è consigliabile seguire questa procedura.
 
1. **Identificare e assegnare ruoli** : identificare i ruoli predefiniti in base alla tabella di mapping precedente e creare ruoli personalizzati quando necessario. Assegnare i ruoli agli ambiti in base alle linee guida per il mapping degli ambiti. Per altre informazioni su come assegnare i ruoli a Key Vault, vedere [fornire l'accesso a Key Vault con un controllo degli accessi in base al ruolo di Azure (anteprima)](rbac-guide.md)
1. **Convalida assegnazione ruoli** : le assegnazioni di ruolo nel controllo degli accessi in base al ruolo di Azure possono richiedere diversi minuti. Per informazioni su come controllare le assegnazioni di ruolo, vedere [elencare le assegnazioni di ruoli nell'ambito](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Configurare il monitoraggio e gli avvisi** per l'insieme di credenziali delle chiavi: è importante abilitare la registrazione e la configurazione degli avvisi per le eccezioni di accesso negato. Per ulteriori informazioni, vedere [monitoraggio e avviso per Azure Key Vault](./alert.md)
1. **Impostare il modello di autorizzazione del controllo degli accessi in base al ruolo di Azure in Key Vault** : l'abilitazione del modello di autorizzazione RBAC di Azure invalida tutti i criteri di accesso esistenti. Se è presente un errore, è possibile cambiare il modello di autorizzazione con tutti i criteri di accesso esistenti rimanenti invariati.

> [!NOTE]
> Quando il modello di autorizzazione RBAC di Azure è abilitato, tutti gli script che tentano di aggiornare i criteri di accesso avranno esito negativo. È importante aggiornare gli script per usare il controllo degli accessi in base al ruolo di Azure.

## <a name="troubleshooting"></a>Risoluzione dei problemi
-  L'assegnazione di ruolo non funziona dopo diversi minuti. in alcune situazioni le assegnazioni di ruolo possono richiedere più tempo. È importante scrivere la logica di ripetizione dei tentativi nel codice per coprire questi casi.
- Le assegnazioni di ruolo sono scomparse quando Key Vault è stato eliminato (eliminazione temporanea) e recuperato. attualmente è una limitazione della funzionalità di eliminazione temporanea in tutti i servizi di Azure. È necessario ricreare tutte le assegnazioni di ruolo dopo il ripristino.    

## <a name="learn-more"></a>Altre informazioni

- [Panoramica di Azure RBAC](../../role-based-access-control/overview.md)
- [Esercitazione sui ruoli personalizzati](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)