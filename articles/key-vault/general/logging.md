---
title: Registrazione di Azure Key Vault | Microsoft Docs
description: Informazioni su come monitorare l'accesso agli insiemi di credenziali delle chiavi mediante l'abilitazione della registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure specificato.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 162e40555e11dff716b58eec4b1168728257693e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131174"
---
# <a name="azure-key-vault-logging"></a>Registrazione di Azure Key Vault

Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. A questo scopo è possibile abilitare la registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure specificato. Per le istruzioni dettagliate sulla configurazione, vedere [Come abilitare la registrazione di Key Vault](howto-logging.md).

È possibile accedere alle informazioni di registrazione dopo massimo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, ma nella maggior parte dei casi si potrà farlo prima.  La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Per informazioni generali su Key Vault, vedere [Cos'è Azure Key Vault?](overview.md). Per informazioni sulla disponibilità di Key Vault, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/key-vault/). Per informazioni sull'uso, vedere [Monitoraggio di Azure per Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="interpret-your-key-vault-logs"></a>Interpretare i log dell'insieme di credenziali delle chiavi

Quando si abilita la registrazione, per l'account di archiviazione specificato viene automaticamente creato un nuovo contenitore denominato **insights-logs-auditevent**. che può essere usato per raccogliere i log relativi a più insiemi di credenziali delle chiavi.

I singoli BLOB vengono archiviati come testo, formattati come BLOB JSON. Ecco un esempio di voce di registro. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

La tabella seguente elenca i nomi dei campi e le descrizioni:

| Nome campo | Descrizione |
| --- | --- |
| **time** |Data e ora in formato UTC. |
| **resourceId** |ID della risorsa di Azure Resource Manager. Per i log dell'insieme di credenziali delle chiavi questo è sempre l'ID della risorsa insieme di credenziali delle chiavi. |
| **operationName** |Nome dell'operazione, come illustrato nella tabella seguente. |
| **operationVersion** |Versione dell'API REST richiesta dal client. |
| **category** |Tipo di risultato. Per i log di Key Vault, `AuditEvent` è il solo valore disponibile. |
| **resultType** |Risultato della richiesta API REST. |
| **resultSignature** |Stato HTTP. |
| **resultDescription** |Descrizione aggiuntiva del risultato, se disponibile. |
| **durationMs** |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| **callerIpAddress** |Indirizzo IP del client che ha eseguito la richiesta. |
| **correlationId** |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| **identity** |Identità del token presentato nella richiesta API REST. In genere si tratta di un "utente", una "entità servizio" o una combinazione "utente+appId", come nel caso di una richiesta generata da un cmdlet di Azure PowerShell. |
| **properties** |Informazioni diverse in base all'operazione (**operationName**). Nella maggior parte dei casi, questo campo contiene informazioni sul client (la stringa agente dell'utente passata dal client), l'URI esatto della richiesta dell'API REST e il codice di stato HTTP. Inoltre, quando un oggetto viene restituito come risultato di una richiesta, ad esempio **KeyCreate** o **VaultGet**, conterrà anche l'URI della chiave (come `id`), l'URI dell'insieme di credenziali o l'URI del segreto. |

I valori del campo **operationName** sono nel formato *OggettoVerbo*. Ad esempio:

* Tutte le operazioni sull'insieme di credenziali delle chiavi hanno il formato `Vault<action>`, ad esempio `VaultGet` e `VaultCreate`.
* Tutte le operazioni sulle chiavi hanno il formato `Key<action>`, ad esempio `KeySign` e `KeyList`.
* Tutte le operazioni sui segreti hanno il formato `Secret<action>`, ad esempio `SecretGet` e `SecretListVersions`.

La tabella seguente include un elenco di valori **operationName** con il comando API REST corrispondente:

### <a name="operation-names-table"></a>Tabella di nomi di operazioni

| operationName | Comando API REST |
| --- | --- |
| **autenticazione** |Eseguire l'autenticazione tramite l'endpoint di Azure Active Directory |
| **VaultGet** |[Ottenere informazioni su un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Creare o aggiornare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Eliminare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Aggiornare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Elencare tutti gli insiemi di credenziali delle chiavi in un gruppo di risorse](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Creare una chiave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Ottenere informazioni su una chiave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importare una chiave in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Eseguire il backup di una chiave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Eliminare una chiave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Ripristinare una chiave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Firmare con una chiave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verificare con una chiave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Eseguire il wrapping di una chiave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Annullare il wrapping di una chiave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Crittografare con una chiave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Decrittografare con una chiave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Aggiornare una chiave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Elencare le chiavi in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Elencare le versioni di una chiave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Creare un segreto](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Ottenere un segreto](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aggiornare un segreto](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Eliminare un segreto](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Elencare i segreti in un insieme di credenziali](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Elencare le versioni di un segreto](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | Evento di modifica dei criteri di accesso all'insieme di credenziali pubblicato |
| **SecretNearExpiryEventGridNotification** |Evento di segreto vicino alla scadenza pubblicato |
| **SecretExpiredEventGridNotification** |Evento di segreto scaduto pubblicato |
| **KeyNearExpiryEventGridNotification** |Evento di chiave vicina alla scadenza pubblicato |
| **KeyExpiredEventGridNotification** |Evento di chiave scaduta pubblicato |
| **CertificateNearExpiryEventGridNotification** |Evento di certificato vicino alla scadenza pubblicato |
| **CertificateExpiredEventGridNotification** |Evento di certificato scaduto pubblicato |

## <a name="use-azure-monitor-logs"></a>Usare i log di Monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di Monitoraggio di Azure per esaminare i log `AuditEvent` di Key Vault. Nei log di Monitoraggio di Azure è possibile usare le query di log per analizzare i dati e ottenere le informazioni necessarie. 

Per altre informazioni, anche su come configurare la soluzione, vedere [Azure Key Vault nei log di Monitoraggio di Azure](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come abilitare la registrazione di Key Vault](howto-logging.md)
- Per un'esercitazione sull'uso di Azure Key Vault in un'applicazione Web .NET, vedere [Usare Azure Key Vault da un'app Web](tutorial-net-create-vault-azure-web-app.md).
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](developers-guide.md).
- Per un elenco di cmdlet di Azure PowerShell 1.0 per Azure Key Vault, vedere [Cmdlet per Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
