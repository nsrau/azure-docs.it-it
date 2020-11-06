---
title: Abilitare la registrazione diagnostica nell'API di Azure per FHIR®
description: Questo articolo illustra come abilitare la registrazione diagnostica nell'API di Azure per FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 262509df98b93c7902d83f90756872a16d84198f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398131"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Abilitare la registrazione diagnostica nell'API di Azure per FHIR®

In questo articolo si apprenderà come abilitare la registrazione diagnostica nell'API di Azure per FHIR® ed essere in grado di esaminare alcune query di esempio per questi log. L'accesso ai log di diagnostica è essenziale per qualsiasi servizio sanitario in cui la conformità ai requisiti normativi (ad esempio HIPAA) è una necessità. La funzionalità dell'API di Azure per FHIR® che Abilita i log di diagnostica sono le [**impostazioni di diagnostica**](../azure-monitor/platform/diagnostic-settings.md) nel portale di Azure. 

## <a name="enable-audit-logs"></a>Abilitare i log di controllo
1. Per abilitare la registrazione diagnostica nell'API di Azure per FHIR®, selezionare l'API di Azure per FHIR servizio® nel portale di Azure 
2. Passa a **impostazioni di diagnostica impostazioni**  
 di diagnostica ![](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. Selezionare **+ Aggiungi impostazione di diagnostica**

4. Immettere un nome per l'impostazione

5. Selezionare il metodo che si vuole usare per accedere ai log di diagnostica:

    1. **Archiviare in un account di archiviazione per il** controllo o l'ispezione manuale. È necessario che l'account di archiviazione che si vuole usare sia già stato creato.
    2. **Trasmettere a hub eventi** per l'inserimento da parte di un servizio di terze parti o di una soluzione analitica personalizzata. Prima di poter configurare questo passaggio, è necessario creare uno spazio dei nomi dell'hub eventi e un criterio dell'hub eventi.
    3. **Trasmettere all'area di lavoro log Analytics** in monitoraggio di Azure. Prima di poter selezionare questa opzione, è necessario creare l'area di lavoro log Analytics.

6. Selezionare **AuditLogs** e tutte le metriche che si desidera acquisire

7. Fare clic su Salva.

> [!Note] 
> Potrebbero essere necessari fino a 15 minuti prima che il primo log venga visualizzato in Log Analytics.  
 
Per altre informazioni su come usare i log di diagnostica, vedere la [documentazione relativa al log delle risorse di Azure](../azure-monitor/platform/platform-logs-overview.md)

## <a name="audit-log-details"></a>Dettagli log di controllo
Al momento, il servizio® API di Azure per FHIR restituisce i campi seguenti nel log di controllo: 

|Nome campo  |Type  |Note  |
|---------|---------|---------|
|CallerIdentity|Dynamic|Contenitore di proprietà generico che contiene informazioni sull'identità
|CallerIdentityIssuer|string|Issuer 
|CallerIdentityObjectId|string|Object_Id 
|CallerIPAddress|string|Indirizzo IP del chiamante 
|CorrelationId|string| ID correlazione
|FhirResourceType|string|Il tipo di risorsa per cui è stata eseguita l'operazione
|LogCategory|string|Categoria log (attualmente in fase di restituzione di ' AuditLogs ' LogCategory)
|Location|string|Il percorso del server che ha elaborato la richiesta (ad esempio, Stati Uniti centro-meridionali)
|OperationDuration|Int|Tempo richiesto per completare la richiesta in pochi secondi
|OperationName|string| Descrive il tipo di operazione (ad esempio, Update, Search-Type)
|RequestUri|string|L'URI della richiesta 
|ResultType|string|I valori disponibili attualmente sono **Started** , **succeeded** o **failed**
|StatusCode|Int|Codice di stato HTTP. (ad esempio, 200) 
|TimeGenerated|Datetime|Data e ora dell'evento|
|Proprietà|string| Descrive le proprietà di fhirResourceType
|SourceSystem|string| Sistema di origine (sempre Azure in questo caso)
|TenantId|string|ID tenant
|Type|string|Tipo di log (sempre MicrosoftHealthcareApisAuditLog in questo caso)
|_ResourceId|string|Dettagli sulla risorsa

## <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate alcune query Application Insights di base che è possibile usare per esplorare i dati di log.

Eseguire la query per visualizzare i **100 log più recenti** :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Eseguire questa query per raggruppare le operazioni per **tipo di risorsa FHIR** :

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Eseguire questa query per ottenere tutti i **risultati non riusciti**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusione 
L'accesso ai log di diagnostica è essenziale per il monitoraggio di un servizio e per la fornitura di report di conformità. API di Azure per FHIR® consente di eseguire queste azioni tramite i log di diagnostica. 
 
FHIR® è il marchio registrato di HL7 e viene usato con l'autorizzazione di HL7.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come abilitare i log di controllo per l'API di Azure per FHIR®. Successivamente, informazioni sulle altre impostazioni che è possibile configurare nell'API di Azure per FHIR
 
>[!div class="nextstepaction"]
>[Impostazioni aggiuntive](azure-api-for-fhir-additional-settings.md)