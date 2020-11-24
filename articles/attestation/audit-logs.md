---
title: Log di controllo di attestazione di Azure
description: Descrive i log di controllo completi raccolti per l'attestazione di Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95758993"
---
# <a name="audit-logs-for-azure-attestation"></a>Log di controllo per l'attestazione di Azure

I log di controllo sono record di eventi discreti, non modificabili e con timestamp, che si sono verificati nel tempo. Questi log acquisiscono eventi importanti che possono influire sulla funzionalità dell'istanza di attestazione.

L'attestazione di Azure gestisce le istanze di attestazione e i criteri associati. Le azioni associate alla gestione delle istanze e alle modifiche ai criteri vengono controllate e registrate.

Questo articolo contiene informazioni sugli eventi registrati, le informazioni raccolte e il percorso di questi log.

## <a name="about-audit-logs"></a>Informazioni sui log di controllo

L'attestazione di Azure usa il codice per produrre log di controllo per gli eventi che influiscono sul modo in cui viene eseguita l'attestazione. Questo in genere si riduce a come o quando vengono apportate modifiche ai criteri all'istanza di attestazione e ad alcune azioni amministrative.

### <a name="auditable-events"></a>Eventi controllabili
Ecco alcuni dei log di controllo raccolti:

|     Evento/API                              |     Descrizione evento                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     creare un'istanza                        |     Crea una nuova istanza di un servizio di attestazione. |
|     Elimina istanza                       |     Elimina un'istanza di un servizio di attestazione. |
|     Aggiungi certificato criteri                 |     Aggiunta di un certificato al set corrente di certificati di gestione dei criteri. |
|     Rimuovi certificato criterio              |     Rimuovere un certificato dal set corrente di certificati di gestione dei criteri. |
|     Imposta criteri correnti                     |     Imposta i criteri di attestazione per un tipo di TEE specificato. |
|     Reimposta criterio di attestazione               |     Reimposta i criteri di attestazione per un tipo di TEE specificato. |
|     Preparare l'aggiornamento dei criteri               |     Preparare l'aggiornamento dei criteri di attestazione per un tipo di TEE specificato. |
|     Reidratare i tenant dopo l'emergenza       |     Chiude nuovamente tutti i tenant di attestazione in questa istanza del servizio di attestazione. Questa operazione può essere eseguita solo dagli amministratori del servizio di attestazione. |

### <a name="collected--information"></a>Informazioni raccolte
Per ognuno di questi eventi, l'attestazione di Azure raccoglie le informazioni seguenti:

- Nome operazione
- Operazione riuscita
- Chiamante dell'operazione, che può essere uno dei seguenti:
    - UPN Azure AD
    - ID dell'oggetto.
    - Certificato
    - Azure AD Tenant ID (ID del tenant di Azure AD)
- Destinazione dell'operazione, che può essere uno dei seguenti:
    - Ambiente
    - Area servizio
    - Ruolo del servizio
    - Istanza del ruolo del servizio
    - ID risorsa
    - Area risorse

### <a name="sample-audit-log"></a>Log di controllo di esempio

I log di controllo sono forniti in formato JSON. Di seguito è riportato un esempio di come potrebbe essere un log di controllo.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Accedi ai log di controllo

Questi log vengono archiviati in Azure e non è possibile accedervi direttamente. Se è necessario accedere a questi log, archiviare un ticket di supporto. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](https://azure.microsoft.com/support/options/). 

Una volta registrato il ticket di supporto, Microsoft scaricherà e fornirà l'accesso a questi log.
