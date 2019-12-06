---
title: Operazioni dei provider di risorse di Azure Resource Manager | Microsoft Docs
description: Elenca le operazioni disponibili per i provider di risorse di Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b00e699eb49d88e41dff18d6c86024c9b0537e50
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872316"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operazioni dei provider di risorse di Azure Resource Manager

Questo articolo elenca le operazioni disponibili per ogni provider di risorse di Azure Resource Manager. Tali operazioni possono essere usate nei [ruoli personalizzati](custom-roles.md) per offrire un [controllo degli accessi in base al ruolo](overview.md) granulare per le risorse in Azure. Le stringhe di operazione hanno il formato seguente: `{Company}.{ProviderName}/{resourceType}/{action}`. Per un elenco delle modalità di mapping tra gli spazi dei nomi del provider di risorse e i servizi di Azure, vedere [associare il provider di risorse al servizio](../azure-resource-manager/azure-services-resource-providers.md).

Le operazioni dei provider di risorse sono costantemente in evoluzione. Per ottenere le operazioni più recenti, usare [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) oppure [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.AAD/domainServices/delete | Elimina un servizio di dominio |
> | Azione | Microsoft.AAD/domainServices/oucontainer/delete | Elimina un contenitore di unità organizzative |
> | Azione | Microsoft.AAD/domainServices/oucontainer/read | Legge i contenitori di unità organizzative |
> | Azione | Microsoft.AAD/domainServices/oucontainer/write | Scrive in un contenitore di unità organizzative |
> | Azione | Microsoft.AAD/domainServices/read | Legge i servizi di dominio |
> | Azione | Microsoft.AAD/domainServices/write | Scrive un servizio di dominio |
> | Azione | Microsoft.AAD/locations/operationresults/read |  |
> | Azione | Microsoft.AAD/Operations/read |  |
> | Azione | Microsoft.AAD/register/action | Registra un servizio di dominio |
> | Azione | Microsoft.AAD/unregister/action | Annulla la registrazione di un servizio di dominio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | microsoft.aadiam/diagnosticsettings/delete | Eliminazione di un'impostazione di diagnostica |
> | Azione | microsoft.aadiam/diagnosticsettings/read | Lettura di un'impostazione di diagnostica |
> | Azione | microsoft.aadiam/diagnosticsettings/write | Scrittura di un'impostazione di diagnostica |
> | Azione | microsoft.aadiam/diagnosticsettingscategories/read | Lettura delle categorie di un'impostazione di diagnostica |
> | Azione | Microsoft. aadiam/metricDefinitions/Read | Lettura delle definizioni delle metriche a livello di tenant |
> | Azione | Microsoft. aadiam/metrica/lettura | Lettura delle metriche a livello di tenant |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Addons/operations/read | Recupera le operazioni del provider di risorse supportate. |
> | Azione | Microsoft.Addons/register/action | Registra la sottoscrizione specificata con Microsoft.Addons |
> | Azione | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Elenca le informazioni relative al piano di supporto corrente per la sottoscrizione specificata. |
> | Azione | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Rimuove il piano di supporto Canonical specificato |
> | Azione | Microsoft.Addons/supportProviders/supportPlanTypes/read | Recupera lo stato del piano di supporto Canonical specificato. |
> | Azione | Microsoft.Addons/supportProviders/supportPlanTypes/write | Aggiunge il tipo di piano di supporto Canonical specificato. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ADHybridHealthService/addsservices/action | Crea una nuova foresta per il tenant. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Recupera tutti i server per il nome di servizio specificato. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/alerts/read | Recupera i dettagli degli avvisi per la foresta, come ID avviso, data di generazione dell'avviso, ultimo avviso rilevato, descrizione dell'avviso, ultimo aggiornamento, livello e stato dell'avviso, collegamenti per la risoluzione dei relativi problemi e così via. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/configuration/read | Recupera la configurazione del servizio per la foresta, ad esempio il nome della foresta, il livello funzionale, il ruolo master di denominazione dei domini FSMO, il ruolo master schema FSMO e così via. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/delete | Elimina un servizio e i relativi server, insieme ai dati di integrità. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Recupera i dettagli relativi ai domani e ai siti per la foresta, ad esempio stato di integrità, avvisi attivi, avvisi risolti, proprietà come livello funzionale del dominio, foresta, master infrastrutture, PDC, master RID e così via.  |
> | Azione | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Recupera l'impostazione delle preferenze utente per la foresta.<br>Ad esempio, MetricCounterName come ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes e ldapsearches.<br>Include anche le impostazioni per i grafici dell'interfaccia utente e così via. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Recupera il riepilogo della foresta specificata, come nome della foresta, numero di dominio inclusi, numero di siti e relativi dettagli e così via. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Recupera l'elenco delle metriche supportate per un determinato servizio.<br>Per il servizio file system distribuito di Azure, ad esempio, blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio ADSync, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Questa API recupera le informazioni relative alle metriche per il servizio specificato.<br>Per AD Federation Services, ad esempio, è possibile usare questa API per recuperare le informazioni su blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio di sincronizzazione, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Questa API recupera l'elenco di tutte le istanze di AD Domain Services di cui è stato eseguito l'onboarding per un tenant Premium. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/read | Recupera i dettagli del servizio per il nome di servizio specificato. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Recupera i dettagli della replica per tutti i server per il nome di servizio specificato. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Recupera il numero di controller di dominio e i relativi errori di replica, se presenti. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Recupera l'elenco completo dei controller di dominio e i dettagli della replica per la foresta specificata. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Aggiunge un'istanza server al servizio. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante la registrazione di server in AD Domain Services, questa API viene chiamata per recuperare le credenziali per l'onboarding di nuovi server. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Elimina un server per il servizio e il tenant specificati. |
> | Azione | Microsoft.ADHybridHealthService/addsservices/write | Crea o aggiorna l'istanza di AD Domain Services per il tenant. |
> | Azione | Microsoft.ADHybridHealthService/configuration/action | Aggiorna la configurazione tenant. |
> | Azione | Microsoft.ADHybridHealthService/configuration/read | Legge la configurazione tenant. |
> | Azione | Microsoft.ADHybridHealthService/configuration/write | Crea una configurazione tenant. |
> | Azione | Microsoft.ADHybridHealthService/logs/contents/read | Recupera il contenuto dei log di installazione e di registrazione dell'agente archiviati nel BLOB. |
> | Azione | Microsoft.ADHybridHealthService/logs/read | Recupera i log di installazione e di registrazione dell'agente per il tenant. |
> | Azione | Microsoft.ADHybridHealthService/operations/read | Recupera l'elenco delle operazioni supportate dal sistema. |
> | Azione | Microsoft.ADHybridHealthService/register/action | Registra il provider di risorse del servizio per l'integrità ibrida di AD e consente la creazione della relativa risorsa servizio. |
> | Azione | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Recupera l'elenco delle aree disponibili, usato nella metodologia DevOps per supportare gli eventi imprevisti dei clienti. |
> | Azione | Microsoft.ADHybridHealthService/reports/badpassword/read | Recupera un elenco dei tentativi di password non validi per tutti gli utenti in Active Directory Federation Services. |
> | Azione | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Recupera l'URI di firma di accesso condiviso del BLOB, contenente lo stato e il risultato finale del processo di report appena accodato per la frequenza dei tentativi di nome utente/password non validi per ID utente e indirizzo IP al giorno per un determinato tenant. |
> | Azione | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Recupera l'elenco dei tenant con consenso della metodologia DevOps. Questa operazione viene in genere usata per l'assistenza clienti. |
> | Azione | Microsoft.ADHybridHealthService/reports/isdevops/read | Recupera un valore che indica se si tratta di un tenant con o senza consenso della metodologia DevOps. |
> | Azione | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Aggiorna l'ID utente (ID oggetto) per il tenant DevOps selezionato. |
> | Azione | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Recupera la distribuzione selezionata per il tenant specificato. |
> | Azione | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Recupera la posizione di archiviazione del tenant corrispondente all'ID tenant specificato. |
> | Azione | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Recupera la posizione geografica da cui verrà eseguito l'accesso ai dati. |
> | Azione | Microsoft.ADHybridHealthService/services/action | Aggiorna un'istanza del servizio nel tenant. |
> | Azione | Microsoft.ADHybridHealthService/services/alerts/read | Legge gli avvisi per un servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/alerts/read | Legge gli avvisi per un servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Verifica se un servizio ha tutti gli elementi necessari per usare la funzionalità corrispondente al nome di funzionalità specificato. |
> | Azione | Microsoft.ADHybridHealthService/services/delete | Elimina un'istanza del servizio nel tenant. |
> | Azione | Microsoft.ADHybridHealthService/services/exporterrors/read | Recupera gli errori di esportazione per un determinato servizio di sincronizzazione. |
> | Azione | Microsoft.ADHybridHealthService/services/exportstatus/read | Recupera lo stato di esportazione per un determinato servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Recupera i commenti sugli avvisi per il servizio e il server specificati. |
> | Azione | Microsoft.ADHybridHealthService/services/metricmetadata/read | Recupera l'elenco delle metriche supportate per un determinato servizio.<br>Per il servizio file system distribuito di Azure, ad esempio, blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio ADSync, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Questa API recupera la media delle metriche per il servizio specificato.<br>Per AD Federation Services, ad esempio, è possibile usare questa API per recuperare le informazioni su blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio di sincronizzazione, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/services/metrics/groups/read | Questa API recupera le informazioni relative alle metriche per il servizio specificato.<br>Per AD Federation Services, ad esempio, è possibile usare questa API per recuperare le informazioni su blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio di sincronizzazione, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Questa API recupera la visualizzazione aggregata delle metriche per il servizio specificato.<br>Per AD Federation Services, ad esempio, è possibile usare questa API per recuperare le informazioni su blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio di sincronizzazione, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Aggiunge o aggiorna una configurazione di monitoraggio per un servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Recupera le configurazioni di monitoraggio per un determinato servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Aggiunge o aggiorna le configurazioni di monitoraggio per un servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/premiumcheck/read | Questa API recupera l'elenco di tutti i servizi di cui è stato eseguito l'onboarding per un tenant Premium. |
> | Azione | Microsoft.ADHybridHealthService/services/read | Legge le istanze del servizio nel tenant. |
> | Azione | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Ottiene tutti gli URI del report IP rischiosi per gli ultimi 7 giorni. |
> | Azione | Microsoft.ADHybridHealthService/services/reports/details/read | Recupera il report dei primi 50 utenti con errori di password errata degli ultimi 7 giorni. |
> | Azione | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Genera un report IP rischioso e restituisce un URI che punta a esso. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/action | Crea un'istanza server nel servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Legge gli avvisi per un server. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante la registrazione di server, questa API viene chiamata per recuperare le credenziali per l'onboarding di nuovi server. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Per un server specificato, questa API recupera un elenco dei tipi di dati caricati dai server e l'ora dell'ultimo caricamento di ognuno. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/delete | Elimina un'istanza server nel servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Recupera i dettagli degli errori di esportazione per un determinato servizio di sincronizzazione. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Questa API recupera le informazioni relative alle metriche per il servizio specificato.<br>Per AD Federation Services, ad esempio, è possibile usare questa API per recuperare le informazioni su blocchi di account Extranet, totale delle richieste non riuscite, richieste di token in attesa (tramite proxy), richieste di token al secondo e così via.<br>Per AD Domain Services, autenticazioni NTLM al secondo, binding LDAP riusciti al secondo, durata dei binding LDAP, thread LDAP attivi, autenticazioni Kerberos al secondo, totale dei thread ATQ e così via.<br>Per il servizio di sincronizzazione, latenza dei profili di esecuzione, connessioni TCP stabilite, byte privati di Insights Agent ed esportazione delle statistiche in Azure AD. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Ottiene l'elenco di connettori e i nomi dei profili di esecuzione per il servizio e il membro del servizio specificati. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/read | Legge un'istanza server nel servizio. |
> | Azione | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Recupera la configurazione del servizio per un determinato tenant. |
> | Azione | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Recupera lo stato di inserimento delle funzionalità nell'elenco elementi consentiti per un determinato tenant. |
> | Azione | Microsoft.ADHybridHealthService/services/write | Aggiorna un'istanza del servizio nel tenant. |
> | Azione | Microsoft.ADHybridHealthService/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse del servizio per l'integrità ibrida di AD. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Advisor/configurations/read | Ottiene le configurazioni |
> | Azione | Microsoft.Advisor/configurations/write | Crea/Aggiorna la configurazione |
> | Azione | Microsoft.Advisor/generateRecommendations/action | Ottiene lo stato dei suggerimenti generati |
> | Azione | Microsoft.Advisor/generateRecommendations/read | Ottiene lo stato dei suggerimenti generati |
> | Azione | Microsoft. Advisor/metadati/lettura | Get Metadata |
> | Azione | Microsoft.Advisor/operations/read | Ottiene le operazioni per Microsoft Advisor |
> | Azione | Microsoft.Advisor/recommendations/available/action | La nuova raccomandazione è disponibile in Microsoft Advisor |
> | Azione | Microsoft.Advisor/recommendations/read | Legge i suggerimenti |
> | Azione | Microsoft.Advisor/recommendations/suppressions/delete | Cancella le eliminazioni |
> | Azione | Microsoft.Advisor/recommendations/suppressions/read | Ottiene le eliminazioni |
> | Azione | Microsoft.Advisor/recommendations/suppressions/write | Crea/aggiorna eliminazioni |
> | Azione | Microsoft.Advisor/register/action | Registra la sottoscrizione per Microsoft Advisor |
> | Azione | Microsoft.Advisor/suppressions/delete | Cancella le eliminazioni |
> | Azione | Microsoft.Advisor/suppressions/read | Ottiene le eliminazioni |
> | Azione | Microsoft.Advisor/suppressions/write | Crea/aggiorna eliminazioni |
> | Azione | Microsoft.Advisor/unregister/action | Annulla la registrazione della sottoscrizione per Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.AlertsManagement/actionRules/delete | Elimina le regole di azione in una determinata sottoscrizione. |
> | Azione | Microsoft.AlertsManagement/actionRules/read | Ottiene tutte le regole di azione per i filtri di input. |
> | Azione | Microsoft.AlertsManagement/actionRules/write | Crea o aggiorna le regole di azione in una determinata sottoscrizione |
> | Azione | Microsoft.AlertsManagement/alerts/changestate/action | Modifica lo stato dell'avviso. |
> | Azione | Microsoft.AlertsManagement/alerts/diagnostics/read | Ottiene tutti i dati di diagnostica per l'avviso |
> | Azione | Microsoft.AlertsManagement/alerts/history/read | Ottiene la cronologia dell'avviso |
> | Azione | Microsoft.AlertsManagement/alerts/read | Ottiene tutti gli avvisi per i filtri di input. |
> | Azione | Microsoft.AlertsManagement/alertsList/read | Ottiene tutti gli avvisi per i filtri di input tra le sottoscrizioni |
> | Azione | Microsoft. AlertsManagement/alertsMetaData/Read | Ottiene i metadati degli avvisi per il parametro di input. |
> | Azione | Microsoft.AlertsManagement/alertsSummary/read | Ottiene il riepilogo degli avvisi |
> | Azione | Microsoft.AlertsManagement/alertsSummaryList/read | Ottiene il riepilogo degli avvisi tra le sottoscrizioni |
> | Azione | Microsoft.AlertsManagement/Operations/read | Legge le operazioni fornite |
> | Azione | Microsoft.AlertsManagement/register/action | Registra la sottoscrizione per la gestione degli avvisi Microsoft |
> | Azione | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Elimina la regola di avviso di Smart detector in una determinata sottoscrizione |
> | Azione | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Ottenere tutte le regole di avviso di rilevamento intelligente per i filtri di input |
> | Azione | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Crea o aggiorna la regola di avviso di Smart detector in una determinata sottoscrizione |
> | Azione | Microsoft.AlertsManagement/smartGroups/changestate/action | Modifica lo stato del gruppo "intelligente". |
> | Azione | Microsoft.AlertsManagement/smartGroups/history/read | Ottiene la cronologia del gruppo intelligente |
> | Azione | Microsoft.AlertsManagement/smartGroups/read | Recupera tutti i gruppi "intelligenti" per i filtri di input. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verifica che il nome dell’Analysis Server specificato sia valido e non in uso. |
> | Azione | Microsoft.AnalysisServices/locations/operationresults/read | Recupera le informazioni del risultato dell'operazione specificata. |
> | Azione | Microsoft.AnalysisServices/locations/operationstatuses/read | Recupera le informazioni dello stato dell'operazione specificata. |
> | Azione | Microsoft.AnalysisServices/operations/read | Recupera le informazioni delle operazioni |
> | Azione | Microsoft.AnalysisServices/register/action | Registra il provider di risorse di Analysis Services. |
> | Azione | Microsoft.AnalysisServices/servers/delete | Elimina l’Analysis Server. |
> | Azione | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Elenca lo stato del gateway associato al server. |
> | Azione | Microsoft.AnalysisServices/servers/read | Recupera le informazioni dell’Analysis Server specificato. |
> | Azione | Microsoft.AnalysisServices/servers/resume/action | Riprende l’Analysis Server. |
> | Azione | Microsoft.AnalysisServices/servers/skus/read | Recupera informazioni sugli SKU disponibili per il server |
> | Azione | Microsoft.AnalysisServices/servers/suspend/action | Sospende l’Analysis Server. |
> | Azione | Microsoft.AnalysisServices/servers/write | Crea o aggiorna l’Analysis Server specificato. |
> | Azione | Microsoft.AnalysisServices/skus/read | Recupera le informazioni degli SKU |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ApiManagement/checkNameAvailability/read | Verifica che il nome del servizio sia disponibile |
> | Azione | Microsoft.ApiManagement/operations/read | Legge tutte le operazioni API disponibili per la risorsa Microsoft.ApiManagement |
> | Azione | Microsoft.ApiManagement/register/action | Registra la sottoscrizione per il provider di risorse Microsoft.ApiManagement |
> | Azione | Microsoft.ApiManagement/reports/read | Ottiene report aggregati per periodi di tempo, area geografica, sviluppatori, prodotti, API, operazioni, sottoscrizione e richiesta. |
> | Azione | Microsoft.ApiManagement/service/apis/delete | Elimina l'API specificata dell'istanza del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/apis/diagnostics/delete | Elimina la diagnostica specificata da un'API. |
> | Azione | Microsoft.ApiManagement/service/apis/diagnostics/read | Elenca tutti i dati di diagnostica di un'API. o ottiene i dettagli della diagnostica per un'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/diagnostics/write | Crea una nuova diagnostica per un'API o ne aggiorna una esistente. o aggiorna i dettagli della diagnostica per un'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Elimina il commento specificato da un problema. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/attachments/read | Elenca tutti gli allegati per il problema associato all'API specificata. o ottiene i dettagli dell'allegato del problema per un'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/attachments/write | Crea un nuovo allegato per il problema in un'API o ne aggiorna uno esistente. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/comments/delete | Elimina il commento specificato da un problema. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/comments/read | Elenca tutti i commenti per il problema associato all'API specificata. o ottiene i dettagli del commento sul problema per un'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/comments/write | Crea un nuovo commento per il problema in un'API o ne aggiorna uno esistente. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/delete | Elimina il problema specificato da un'API. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/read | Elenca tutti i problemi associati all'API specificata. o ottiene i dettagli del problema per un'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/issues/write | Crea un nuovo problema per un'API o ne aggiorna uno esistente. o aggiorna un problema esistente per un'API. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/delete | Elimina l'operazione specificata nell'API. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/policies/delete | Elimina la configurazione dei criteri nell'operazione API. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/policies/read | Ottiene l'elenco di configurazione dei criteri a livello di operazione API. oppure ottenere la configurazione dei criteri a livello di operazione API. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/policies/write | Crea o aggiorna la configurazione dei criteri per il livello di operazione API. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/policy/delete | Elimina la configurazione dei criteri a livello di operazione |
> | Azione | Microsoft.ApiManagement/service/apis/operations/policy/read | Ottenere la configurazione dei criteri a livello di operazione |
> | Azione | Microsoft.ApiManagement/service/apis/operations/policy/write | Crea configurazione dei criteri a livello di operazione |
> | Azione | Microsoft.ApiManagement/service/apis/operations/read | Elenca una raccolta di operazioni per l'API specificata. o ottiene i dettagli dell'operazione API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/tags/delete | Scollegare il tag dall'operazione. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/tags/read | Elenca tutti i tag associati all'operazione. o Get tag associato all'operazione. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/tags/write | Assegnare un tag all'operazione. |
> | Azione | Microsoft.ApiManagement/service/apis/operations/write | Crea una nuova operazione nell'API o ne aggiorna una esistente. o aggiorna i dettagli dell'operazione nell'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/operationsByTags/read | Elenca una raccolta di operazioni associate ai tag. |
> | Azione | Microsoft.ApiManagement/service/apis/policies/delete | Elimina la configurazione dei criteri nell'API. |
> | Azione | Microsoft.ApiManagement/service/apis/policies/read | Ottenere la configurazione dei criteri a livello di API. oppure ottenere la configurazione dei criteri a livello di API. |
> | Azione | Microsoft.ApiManagement/service/apis/policies/write | Crea o aggiorna la configurazione dei criteri per l'API. |
> | Azione | Microsoft.ApiManagement/service/apis/policy/delete | Elimina la configurazione dei criteri a livello di API |
> | Azione | Microsoft.ApiManagement/service/apis/policy/read | Ottenere la configurazione dei criteri a livello di API |
> | Azione | Microsoft.ApiManagement/service/apis/policy/write | Crea configurazione dei criteri a livello di API |
> | Azione | Microsoft.ApiManagement/service/apis/products/read | Elenca tutti i prodotti di cui fa parte l'API. |
> | Azione | Microsoft.ApiManagement/service/apis/read | Elenca tutte le API dell'istanza del servizio gestione API. o ottiene i dettagli dell'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/releases/delete | Rimuove tutte le versioni dell'API o Elimina la versione specificata nell'API. |
> | Azione | Microsoft.ApiManagement/service/apis/releases/read | Elenca tutte le versioni di un'API.<br>Una versione API viene creata quando si esegue una revisione API corrente.<br>Le versioni vengono usate anche per eseguire il rollback alle revisioni precedenti.<br>I risultati verranno sottoposto a paging e potranno essere vincolati dal $top e $skip parametri.<br>o restituisce i dettagli di una versione API. |
> | Azione | Microsoft.ApiManagement/service/apis/releases/write | Crea una nuova versione per l'API. o aggiorna i dettagli della versione dell'API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apis/revisions/delete | Rimuove tutte le revisioni di un'API |
> | Azione | Microsoft.ApiManagement/service/apis/revisions/read | Elenca tutte le revisioni di un'API. |
> | Azione | Microsoft.ApiManagement/service/apis/schemas/delete | Elimina la configurazione dello schema nell'API. |
> | Azione | Microsoft.ApiManagement/service/apis/schemas/read | Ottenere la configurazione dello schema a livello di API. oppure ottenere la configurazione dello schema a livello di API. |
> | Azione | Microsoft.ApiManagement/service/apis/schemas/write | Crea o aggiorna la configurazione dello schema per l'API. |
> | Azione | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Elimina la descrizione del tag per l'API. |
> | Azione | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Elenca tutte le descrizioni dei tag nell'ambito dell'API. Un modello simile a spavalderia-tagDescription è definito a livello di API, ma il tag può essere assegnato alle operazioni o ottenere la descrizione del tag nell'ambito dell'API |
> | Azione | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Crea/aggiorna la descrizione del tag nell'ambito dell'API. |
> | Azione | Microsoft.ApiManagement/service/apis/tags/delete | Scollegare il tag dall'API. |
> | Azione | Microsoft.ApiManagement/service/apis/tags/read | Elenca tutti i tag associati all'API. o ottenere un tag associato all'API. |
> | Azione | Microsoft.ApiManagement/service/apis/tags/write | Assegnare un tag all'API. |
> | Azione | Microsoft.ApiManagement/service/apis/write | Crea un nuovo o aggiorna l'API specificata esistente dell'istanza del servizio gestione API. o aggiorna l'API specificata dell'istanza del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/apisByTags/read | Elenca una raccolta di API associate ai tag. |
> | Azione | Microsoft.ApiManagement/service/apiVersionSets/delete | Elimina il set di versioni dell'API specifico. |
> | Azione | Microsoft.ApiManagement/service/apiVersionSets/read | Elenca una raccolta di set di versioni dell'API nell'istanza del servizio specificata. o ottiene i dettagli del set di versioni dell'API specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Ottiene l'elenco delle entità versione |
> | Azione | Microsoft.ApiManagement/service/apiVersionSets/write | Crea o aggiorna un set di versioni dell'API. in alternativa, aggiorna i dettagli relativi al versione API specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Aggiorna le risorse Microsoft.ApiManagement in esecuzione nella rete virtuale per selezionare le impostazioni di rete aggiornate. |
> | Azione | Microsoft.ApiManagement/service/authorizationServers/delete | Elimina un'istanza del server di autorizzazione specifica. |
> | Azione | Microsoft. ApiManagement/Service/authorizationServers/listSecrets/Action | Ottiene i segreti per il server di autorizzazione. |
> | Azione | Microsoft.ApiManagement/service/authorizationServers/read | Elenca una raccolta di server di autorizzazione definiti in un'istanza del servizio. o ottiene i dettagli del server di autorizzazione senza segreti. |
> | Azione | Microsoft.ApiManagement/service/authorizationServers/write | Consente di creare un nuovo server di autorizzazione o di aggiornare un server di autorizzazione esistente. o aggiorna i dettagli del server di autorizzazione specificato in base al relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/backends/delete | Elimina il back-end specificato. |
> | Azione | Microsoft.ApiManagement/service/backends/read | Elenca una raccolta di backend nell'istanza del servizio specificata. o ottiene i dettagli del back-end specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/backends/reconnect/action | Notifica al proxy gestione API di creare una nuova connessione al back-end dopo il timeout specificato. Se non è stato specificato alcun timeout, viene usato il timeout di 2 minuti. |
> | Azione | Microsoft.ApiManagement/service/backends/write | Crea o aggiorna un back-end. o aggiorna un back-end esistente. |
> | Azione | Microsoft.ApiManagement/service/backup/action | Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente |
> | Azione | Microsoft.ApiManagement/service/caches/delete | Elimina la cache specifica. |
> | Azione | Microsoft.ApiManagement/service/caches/read | Elenca una raccolta di tutte le cache esterne nell'istanza del servizio specificata. o ottiene i dettagli della cache specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/caches/write | Crea o aggiorna una cache esterna da usare nell'istanza di gestione API. o aggiorna i dettagli della cache specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/certificates/delete | Elimina un certificato specifico. |
> | Azione | Microsoft.ApiManagement/service/certificates/read | Elenca una raccolta di tutti i certificati nell'istanza del servizio specificata. o ottiene i dettagli del certificato specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/certificates/write | Crea o aggiorna il certificato usato per l'autenticazione con il back-end. |
> | Azione | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Rimuove l'elemento di contenuto specificato. |
> | Azione | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Restituisce l'elenco degli elementi di contenuto o restituisce i dettagli dell'elemento di contenuto |
> | Azione | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Crea nuovo elemento di contenuto o aggiorna l'elemento di contenuto specificato |
> | Azione | Microsoft.ApiManagement/service/contentTypes/read | Restituisce l'elenco dei tipi di contenuto |
> | Azione | Microsoft.ApiManagement/service/delete | Elimina l’istanza del servizio Gestione API |
> | Azione | Microsoft.ApiManagement/service/diagnostics/delete | Elimina la diagnostica specificata. |
> | Azione | Microsoft.ApiManagement/service/diagnostics/read | Elenca tutte le diagnostica dell'istanza del servizio gestione API. o ottiene i dettagli della diagnostica specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/diagnostics/write | Crea una nuova diagnostica o ne aggiorna una esistente. o aggiorna i dettagli della diagnostica specificata dal relativo identificatore. |
> | Azione | Microsoft. ApiManagement/Service/gateway/azione | Recupera la configurazione del gateway. o aggiorna l'heartbeat del gateway. |
> | Azione | Microsoft. ApiManagement/Service/gateway/API/Delete | Elimina l'API specificata dal gateway specificato. |
> | Azione | Microsoft. ApiManagement/Service/gateway/API/lettura | Elenca una raccolta di API associate a un gateway. |
> | Azione | Microsoft. ApiManagement/Service/gateway/API/scrittura | Aggiunge un'API al gateway specificato. |
> | Azione | Microsoft. ApiManagement/Service/gateway/Delete | Elimina un gateway specifico. |
> | Azione | Microsoft. ApiManagement/Service/Gateways/hostnameConfigurations/Read | Elenca la raccolta di configurazioni del nome host per il gateway specificato. |
> | Azione | Microsoft. ApiManagement/Service/gateway/chiavi/azione | Recupera le chiavi del gateway. |
> | Azione | Microsoft. ApiManagement/Service/gateway/lettura | Elenca una raccolta di gateway registrati con l'istanza del servizio. o ottiene i dettagli del gateway specificato dal relativo identificatore. |
> | Azione | Microsoft. ApiManagement/Service/Gateways/regeneratePrimaryKey/Action | Rigenera la chiave del gateway primaria invalidationg tutti i token creati con esso. |
> | Azione | Microsoft. ApiManagement/Service/Gateways/regenerateSecondaryKey/Action | Rigenera la chiave del gateway secondaria invalidationg tutti i token creati con esso. |
> | Azione | Microsoft. ApiManagement/Service/gateway/token/azione | Ottiene il token di autorizzazione di accesso condiviso per il gateway. |
> | Azione | Microsoft. ApiManagement/Service/gateway/scrittura | Crea o aggiorna un gateway da usare nell'istanza di gestione API. o aggiorna i dettagli del gateway specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/getssotoken/action | Ottiene il token SSO che può essere utilizzato per l'accesso al portale legacy del servizio Gestione API come amministratore |
> | Azione | Microsoft.ApiManagement/service/groups/delete | Elimina un gruppo specifico dell'istanza del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/groups/read | Elenca una raccolta di gruppi definiti in un'istanza del servizio. o ottiene i dettagli del gruppo specificato in base al relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/groups/users/delete | Rimuovere l'utente esistente dal gruppo esistente. |
> | Azione | Microsoft.ApiManagement/service/groups/users/read | Elenca una raccolta di entità utente associate al gruppo. |
> | Azione | Microsoft.ApiManagement/service/groups/users/write | Aggiunge l’utente esistente al gruppo esistente |
> | Azione | Microsoft.ApiManagement/service/groups/write | Crea o aggiorna un gruppo. o aggiorna i dettagli del gruppo specificato in base al relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/identityProviders/delete | Elimina la configurazione del provider di identità specificato. |
> | Azione | Microsoft. ApiManagement/Service/non/listSecrets/Action | Ottiene i segreti del provider di identità. |
> | Azione | Microsoft.ApiManagement/service/identityProviders/read | Elenca una raccolta di provider di identità configurato nell'istanza del servizio specificata. o ottiene i dettagli di configurazione del provider di identità senza segreti. |
> | Azione | Microsoft.ApiManagement/service/identityProviders/write | Crea o aggiorna la configurazione IdentityProvider. o aggiorna una configurazione di IdentityProvider esistente. |
> | Azione | Microsoft.ApiManagement/service/issues/read | Elenca una raccolta di problemi nell'istanza del servizio specificata. o ottiene i dettagli del problema di gestione API |
> | Azione | Microsoft.ApiManagement/service/locations/networkstatus/read | Ottiene lo stato di accesso di rete delle risorse da cui il servizio dipende nella posizione. |
> | Azione | Microsoft.ApiManagement/service/loggers/delete | Elimina il logger specificato. |
> | Azione | Microsoft.ApiManagement/service/loggers/read | Elenca una raccolta di logger nell'istanza del servizio specificata. o ottiene i dettagli del logger specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/loggers/write | Crea o aggiorna un logger. o aggiorna un logger esistente. |
> | Azione | Microsoft.ApiManagement/service/managedeployments/action | Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API |
> | Azione | Microsoft. ApiManagement/Service/namedValues/Delete | Elimina un valore denominato specifico dall'istanza del servizio gestione API. |
> | Azione | Microsoft. ApiManagement/Service/namedValues/listSecrets/Action | Ottiene i segreti del valore denominato specificato dal relativo identificatore. |
> | Azione | Microsoft. ApiManagement/Service/namedValues/Read | Elenca una raccolta di valori denominati definiti all'interno di un'istanza del servizio. o ottiene i dettagli del valore denominato specificato dal relativo identificatore. |
> | Azione | Microsoft. ApiManagement/Service/namedValues/Write | Crea o aggiorna un valore denominato. o aggiorna il valore denominato specifico. |
> | Azione | Microsoft.ApiManagement/service/networkstatus/read | Ottiene lo stato di accesso di rete delle risorse da cui il servizio dipende. |
> | Azione | Microsoft.ApiManagement/service/notifications/action | Invia notifiche a un utente specifico |
> | Azione | Microsoft.ApiManagement/service/notifications/read | Elenca una raccolta di proprietà definite in un'istanza del servizio. o ottiene i dettagli della notifica specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Rimuove l'indirizzo di posta elettronica dall'elenco di notifiche. |
> | Azione | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Ottiene l'elenco dei messaggi di posta elettronica del destinatario della notifica sottoscritti a una notifica. |
> | Azione | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Aggiunge l'indirizzo di posta elettronica all'elenco di destinatari per la notifica. |
> | Azione | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Rimuove l'utente di gestione API dall'elenco di notifiche. |
> | Azione | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Ottiene l'elenco dell'utente del destinatario della notifica che ha sottoscritto la notifica. |
> | Azione | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Aggiunge l'utente di gestione API all'elenco di destinatari per la notifica. |
> | Azione | Microsoft.ApiManagement/service/notifications/write | Crea o aggiorna la notifica del server di pubblicazione di gestione API. |
> | Azione | Microsoft.ApiManagement/service/openidConnectProviders/delete | Elimina il provider OpenID Connect specifico dell'istanza del servizio gestione API. |
> | Azione | Microsoft. ApiManagement/Service/openidConnectProviders/listSecrets/Action | Ottiene i segreti specifici del provider OpenID Connect. |
> | Azione | Microsoft.ApiManagement/service/openidConnectProviders/read | Elenchi di tutti i provider OpenId Connect. o ottiene un provider OpenID Connect specifico senza segreti. |
> | Azione | Microsoft.ApiManagement/service/openidConnectProviders/write | Crea o aggiorna il provider OpenID Connect. o aggiorna il provider OpenID Connect specifico. |
> | Azione | Microsoft.ApiManagement/service/operationresults/read | Ottiene lo stato corrente dell'operazione in esecuzione prolungata |
> | Azione | Microsoft.ApiManagement/service/policies/delete | Elimina la configurazione dei criteri globali del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/policies/read | Elenca tutte le definizioni di criteri globali del servizio gestione API. oppure ottenere la definizione di criteri globali del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/policies/write | Crea o aggiorna la configurazione dei criteri globali del servizio gestione API. |
> | Azione | Microsoft. ApiManagement/Service/Policy/Delete | Elimina la configurazione dei criteri a livello di tenant |
> | Azione | Microsoft.ApiManagement/service/policy/read | Ottenere la configurazione dei criteri a livello di tenant |
> | Azione | Microsoft. ApiManagement/Service/Policy/Write | Crea configurazione dei criteri a livello di tenant |
> | Azione | Microsoft. ApiManagement/Service/policyDescriptions/Read | Elenca tutte le descrizioni dei criteri. |
> | Azione | Microsoft.ApiManagement/service/policySnippets/read | Elenca tutti i frammenti di criteri. |
> | Azione | Microsoft.ApiManagement/service/portalsettings/read | Ottenere le impostazioni di accesso per il portale o ottenere le impostazioni di registrazione per il portale o ottenere le impostazioni di delega per il portale. |
> | Azione | Microsoft.ApiManagement/service/portalsettings/write | Aggiornare le impostazioni di accesso. oppure creare o aggiornare le impostazioni di accesso. o aggiornare le impostazioni di iscrizione o aggiornare le impostazioni di iscrizione o aggiornare le impostazioni di delega. oppure creare o aggiornare le impostazioni di delega. |
> | Azione | Microsoft.ApiManagement/service/products/apis/delete | Elimina l'API specificata dal prodotto specificato. |
> | Azione | Microsoft.ApiManagement/service/products/apis/read | Elenca una raccolta di API associate a un prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/apis/write | Aggiunge un'API al prodotto specificato. |
> | Azione | Microsoft.ApiManagement/service/products/delete | Elimina prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/groups/delete | Elimina l'associazione tra il gruppo e il prodotto specificati. |
> | Azione | Microsoft.ApiManagement/service/products/groups/read | Elenca la raccolta di gruppi di sviluppatori associati al prodotto specificato. |
> | Azione | Microsoft.ApiManagement/service/products/groups/write | Aggiunge l'associazione tra il gruppo di sviluppatori specificato e il prodotto specificato. |
> | Azione | Microsoft.ApiManagement/service/products/policies/delete | Elimina la configurazione dei criteri nel prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/policies/read | Ottenere la configurazione dei criteri a livello di prodotto. oppure ottenere la configurazione dei criteri a livello di prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/policies/write | Crea o aggiorna la configurazione dei criteri per il prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/policy/delete | Elimina la configurazione dei criteri a livello di prodotto |
> | Azione | Microsoft.ApiManagement/service/products/policy/read | Ottenere la configurazione dei criteri a livello di prodotto |
> | Azione | Microsoft.ApiManagement/service/products/policy/write | Crea configurazione dei criteri a livello di prodotto |
> | Azione | Microsoft.ApiManagement/service/products/read | Elenca una raccolta di prodotti nell'istanza del servizio specificata. o ottiene i dettagli del prodotto specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/products/subscriptions/read | Elenca la raccolta di sottoscrizioni al prodotto specificato. |
> | Azione | Microsoft.ApiManagement/service/products/tags/delete | Scollegare il tag dal prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/tags/read | Elenca tutti i tag associati al prodotto. o Get tag associato al prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/tags/write | Assegnare un tag al prodotto. |
> | Azione | Microsoft.ApiManagement/service/products/write | Crea o aggiorna un prodotto. oppure aggiornare i dettagli del prodotto esistenti. |
> | Azione | Microsoft.ApiManagement/service/productsByTags/read | Elenca una raccolta di prodotti associati ai tag. |
> | Azione | Microsoft.ApiManagement/service/properties/delete | Elimina la proprietà specifica dall'istanza del servizio gestione API. |
> | Azione | Microsoft. ApiManagement/Service/Properties/listSecrets/Action | Ottiene i segreti della proprietà specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/properties/read | Elenca una raccolta di proprietà definite in un'istanza del servizio. o ottiene i dettagli della proprietà specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/properties/write | Crea o aggiorna una proprietà. o aggiorna la proprietà specifica. |
> | Azione | Microsoft.ApiManagement/service/quotas/periods/read | Ottiene il valore del contatore di quote per il periodo |
> | Azione | Microsoft.ApiManagement/service/quotas/periods/write | Imposta il valore corrente del contatore di quote |
> | Azione | Microsoft.ApiManagement/service/quotas/read | Ottiene i valori per la quota |
> | Azione | Microsoft.ApiManagement/service/quotas/write | Imposta il valore corrente del contatore di quote |
> | Azione | Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | Azione | Microsoft.ApiManagement/service/regions/read | Elenca tutte le aree di Azure in cui è presente il servizio. |
> | Azione | Microsoft.ApiManagement/service/reports/read | Ottiene report aggregati per periodi di tempo o ottiene un report aggregato per area geografica o ottiene un report aggregato per sviluppatori.<br>o ottiene un report aggregato per prodotti.<br>o ottiene un report aggregato per API o ottiene un report aggregato per operazioni o ottiene un report aggregato per sottoscrizione.<br>o ottiene i dati dei report sulle richieste |
> | Azione | Microsoft.ApiManagement/service/restore/action | Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente |
> | Azione | Microsoft.ApiManagement/service/subscriptions/delete | Elimina la sottoscrizione specificata. |
> | Azione | Microsoft. ApiManagement/service/subscriptions/listSecrets/Action | Ottiene le chiavi di sottoscrizione specificate. |
> | Azione | Microsoft.ApiManagement/service/subscriptions/read | Elenca tutte le sottoscrizioni dell'istanza del servizio gestione API. o ottiene l'entità di sottoscrizione specificata (senza chiavi). |
> | Azione | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Rigenera la chiave primaria della sottoscrizione esistente dell'istanza del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Rigenera la chiave secondaria della sottoscrizione esistente dell'istanza del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/subscriptions/write | Crea o aggiorna la sottoscrizione dell'utente specificato al prodotto specificato. o aggiorna i dettagli di una sottoscrizione specificata dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/tagResources/read | Elenca una raccolta di risorse associate ai tag. |
> | Azione | Microsoft.ApiManagement/service/tags/delete | Elimina un tag specifico dell'istanza del servizio gestione API. |
> | Azione | Microsoft.ApiManagement/service/tags/read | Elenca una raccolta di tag definiti all'interno di un'istanza del servizio. o ottiene i dettagli del tag specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/tags/write | Crea un tag. o aggiorna i dettagli del tag specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/templates/delete | Reimposta il modello di posta elettronica predefinito di Gestione API |
> | Azione | Microsoft.ApiManagement/service/templates/read | Ottiene tutti i modelli di posta elettronica o ottiene i dettagli del modello di posta elettronica di Gestione API |
> | Azione | Microsoft.ApiManagement/service/templates/write | Crea o aggiorna il modello di posta elettronica di Gestione API o aggiorna il modello di posta elettronica di Gestione API |
> | Azione | Microsoft.ApiManagement/service/tenant/delete | Rimuove la configurazione dei criteri per il tenant |
> | Azione | Microsoft.ApiManagement/service/tenant/deploy/action | Esegue un'attività di distribuzione per applicare le modifiche dal ramo git specificato alla configurazione sul database. |
> | Azione | Microsoft. ApiManagement/Service/tenant/listSecrets/azione | Ottiene i dettagli sulle informazioni di accesso del tenant |
> | Azione | Microsoft.ApiManagement/service/tenant/operationResults/read | Ottiene l'elenco dei risultati dell'operazione o il risultato di un'operazione specifica |
> | Azione | Microsoft.ApiManagement/service/tenant/read | Ottenere la definizione di criteri globali del servizio gestione API. in alternativa, ottenere i dettagli delle informazioni di accesso al tenant |
> | Azione | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Rigenera la chiave di accesso primaria |
> | Azione | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Rigenera la chiave di accesso secondaria |
> | Azione | Microsoft.ApiManagement/service/tenant/save/action | Crea commit con la snapshot di configurazione sul ramo specificato in archivio |
> | Azione | Microsoft.ApiManagement/service/tenant/syncState/read | Ottiene lo stato dell’ultima sincronizzazione git |
> | Azione | Microsoft.ApiManagement/service/tenant/validate/action | Convalida le modifiche dal ramo git specificato |
> | Azione | Microsoft.ApiManagement/service/tenant/write | Imposta la configurazione dei criteri per il tenant o aggiorna i dettagli di accesso per il tenant |
> | Azione | Microsoft.ApiManagement/service/updatecertificate/action | Carica il certificato SSL per un servizio Gestione API. |
> | Azione | Microsoft.ApiManagement/service/updatehostname/action | Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API. |
> | Azione | Microsoft.ApiManagement/service/users/action | Registra un nuovo utente |
> | Azione | Microsoft.ApiManagement/service/users/confirmations/send/action | Invia una conferma |
> | Azione | Microsoft.ApiManagement/service/users/delete | Elimina un utente specifico. |
> | Azione | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Recupera un URL di reindirizzamento contenente un token di autenticazione per la firma di un determinato utente nel portale per sviluppatori. |
> | Azione | Microsoft.ApiManagement/service/users/groups/read | Elenca tutti i gruppi di utenti. |
> | Azione | Microsoft.ApiManagement/service/users/identities/read | Elenco di tutte le identità utente. |
> | Azione | Microsoft.ApiManagement/service/users/keys/read | Ottenere le chiavi associate all'utente |
> | Azione | Microsoft.ApiManagement/service/users/read | Elenca una raccolta di utenti registrati nell'istanza del servizio specificata. o ottiene i dettagli dell'utente specificato dal relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/users/subscriptions/read | Elenca la raccolta di sottoscrizioni dell'utente specificato. |
> | Azione | Microsoft.ApiManagement/service/users/token/action | Ottiene il token di autorizzazione di accesso condiviso per l'utente. |
> | Azione | Microsoft.ApiManagement/service/users/write | Crea o aggiorna un utente. o aggiorna i dettagli dell'utente specificato in base al relativo identificatore. |
> | Azione | Microsoft.ApiManagement/service/write | Crea una nuova istanza del servizio Gestione API |
> | Azione | Microsoft.ApiManagement/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse Microsoft.ApiManagement. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Authorization/classicAdministrators/delete | Rimuove l'amministratore dalla sottoscrizione. |
> | Azione | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Ottiene gli stati operativi dell'amministratore per la sottoscrizione. |
> | Azione | Microsoft.Authorization/classicAdministrators/read | Esegue la lettura degli amministratori per la sottoscrizione. |
> | Azione | Microsoft.Authorization/classicAdministrators/write | Aggiunge o modifica l'amministratore in una sottoscrizione. |
> | Azione | Microsoft.Authorization/denyAssignments/delete | Consente di eliminare un'assegnazione di rifiuto nell'ambito specificato. |
> | Azione | Microsoft.Authorization/denyAssignments/read | Consente di ottenere informazioni su un'assegnazione di rifiuto. |
> | Azione | Microsoft.Authorization/denyAssignments/write | Consente di creare un'assegnazione di rifiuto nell'ambito specificato. |
> | Azione | Microsoft.Authorization/elevateAccess/action | Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant |
> | Azione | Microsoft.Authorization/locks/delete | Elimina i blocchi per l'ambito specificato. |
> | Azione | Microsoft.Authorization/locks/read | Ottiene i blocchi per l'ambito specificato. |
> | Azione | Microsoft.Authorization/locks/write | Aggiunge i blocchi per l'ambito specificato. |
> | Azione | Microsoft.Authorization/operations/read | Ottiene l'elenco delle operazioni |
> | Azione | Microsoft.Authorization/permissions/read | Elenca tutte le autorizzazioni del chiamante per un ambito specifico. |
> | Azione | Microsoft. Authorization/Policies/audit/Action | Azione eseguita come risultato della valutazione dei criteri di Azure con effetto ' audit ' |
> | Azione | Microsoft. Authorization/Policies/auditIfNotExists/Action | Azione eseguita come risultato della valutazione dei criteri di Azure con effetto ' auditIfNotExists ' |
> | Azione | Microsoft. Authorization/Policies/Deny/Action | Azione eseguita come risultato della valutazione dei criteri di Azure con effetto ' Deny ' |
> | Azione | Microsoft. Authorization/Policies/deployIfNotExists/Action | Azione eseguita come risultato della valutazione dei criteri di Azure con effetto ' deployIfNotExists ' |
> | Azione | Microsoft.Authorization/policyAssignments/delete | Elimina un'assegnazione di criteri per l'ambito specificato. |
> | Azione | Microsoft.Authorization/policyAssignments/read | Ottiene informazioni su un'assegnazione di criteri. |
> | Azione | Microsoft.Authorization/policyAssignments/write | Crea un'assegnazione di criteri per l'ambito specificato. |
> | Azione | Microsoft.Authorization/policyDefinitions/delete | Elimina una definizione criteri. |
> | Azione | Microsoft.Authorization/policyDefinitions/read | Ottiene informazioni su una definizione di criteri. |
> | Azione | Microsoft.Authorization/policyDefinitions/write | Crea una definizione di criteri personalizzata. |
> | Azione | Microsoft.Authorization/policySetDefinitions/delete | Elimina una definizione di set di criteri. |
> | Azione | Microsoft.Authorization/policySetDefinitions/read | Ottiene informazioni su una definizione di set di criteri. |
> | Azione | Microsoft.Authorization/policySetDefinitions/write | Crea una definizione di set di criteri personalizzata. |
> | Azione | Microsoft.Authorization/providerOperations/read | Ottiene operazioni per tutti i provider di risorse che possono essere usati nelle definizioni del ruolo. |
> | Azione | Microsoft.Authorization/roleAssignments/delete | È possibile eliminare un'assegnazione di ruolo nell'ambito specificato. |
> | Azione | Microsoft.Authorization/roleAssignments/read | Ottiene informazioni su un'assegnazione di ruolo. |
> | Azione | Microsoft.Authorization/roleAssignments/write | Crea un'assegnazione di ruolo per l'ambito specificato. |
> | Azione | Microsoft.Authorization/roleDefinitions/delete | Elimina la definizione del ruolo personalizzata specificata. |
> | Azione | Microsoft.Authorization/roleDefinitions/read | Ottiene informazioni su una definizione di ruolo. |
> | Azione | Microsoft.Authorization/roleDefinitions/write | Crea o aggiorna una definizione del ruolo personalizzata con le autorizzazioni specificate e gli ambiti assegnabili. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Legge le informazioni di registrazione di un DSC di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Scrive una richiesta di rigenerazione delle chiavi di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/certificates/delete | Elimina un asset del certificato di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/certificates/getCount/action | Legge il numero di certificati |
> | Azione | Microsoft.Automation/automationAccounts/certificates/read | Ottiene un asset del certificato di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/certificates/write | Crea o aggiorna un asset del certificato di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/compilationjobs/read | Legge una compilazione Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/compilationjobs/read | Legge una compilazione Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/compilationjobs/write | Scrive una compilazione Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/compilationjobs/write | Scrive una compilazione Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/configurations/content/read | Legge il contenuto multimediale della configurazione |
> | Azione | Microsoft.Automation/automationAccounts/configurations/delete | Elimina un contenuto di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/configurations/getCount/action | Legge il conteggio di un contenuto di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/configurations/read | Ottiene un contenuto di Automation DSC per Azure |
> | Azione | Microsoft.Automation/automationAccounts/configurations/write | Scrive un contenuto di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/connections/delete | Elimina un asset della connessione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/connections/getCount/action | Legge il numero di connessioni |
> | Azione | Microsoft.Automation/automationAccounts/connections/read | Ottiene un asset della connessione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/connections/write | Crea o aggiorna un asset della connessione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/connectionTypes/delete | Elimina un asset del tipo di connessione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/connectionTypes/read | Ottiene un asset del tipo di connessione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/connectionTypes/write | Crea un asset del tipo di connessione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/credentials/delete | Elimina un asset delle credenziali di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/credentials/getCount/action | Legge il numero di credenziali |
> | Azione | Microsoft.Automation/automationAccounts/credentials/read | Ottiene un asset delle credenziali di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/credentials/write | Crea o aggiorna un asset delle credenziali di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/delete | Elimina un account di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Elimina le risorse del ruolo di lavoro ibrido per runbook |
> | Azione | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | Azione | Microsoft.Automation/automationAccounts/jobs/output/read | Ottiene l'output di un processo |
> | Azione | Microsoft.Automation/automationAccounts/jobs/read | Ottiene un processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobs/resume/action | Riprende un processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Ottiene il contenuto del runbook di automazione di Azure durante l'esecuzione del processo |
> | Azione | Microsoft.Automation/automationAccounts/jobs/stop/action | Arresta un processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobs/suspend/action | Sospende un processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobs/write | Crea un processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobSchedules/delete | Elimina una pianificazione del processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobSchedules/read | Ottiene una pianificazione del processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/jobSchedules/write | Crea una pianificazione del processo di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Ottiene l'area di lavoro collegata all'account di automazione |
> | Azione | Microsoft.Automation/automationAccounts/listKeys/action | Legge le chiavi per l'account di automazione |
> | Azione | Microsoft.Automation/automationAccounts/modules/activities/read | Ottiene le attività di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/modules/delete | Elimina un modulo PowerShell di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/modules/getCount/action | Recupera il numero di moduli PowerShell all'interno dell'account di automazione |
> | Azione | Microsoft.Automation/automationAccounts/modules/read | Ottiene un modulo PowerShell di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/modules/write | Crea o aggiorna un modulo PowerShell di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Elimina una configurazione nodo di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Legge il contenuto di una configurazione nodo di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Legge una configurazione nodo di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Scrive una configurazione nodo di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodecounts/read | Legge il riepilogo del conteggio dei nodi per il tipo specificato |
> | Azione | Microsoft.Automation/automationAccounts/nodes/delete | Elimina i nodi di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodes/read | Legge i nodi di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Legge il contenuto dei report di Automation DSC per Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodes/reports/read | Legge i report di Automation DSC di Azure |
> | Azione | Microsoft.Automation/automationAccounts/nodes/write | Crea o aggiorna i nodi di Automation DSC per Azure |
> | Azione | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Ottiene i TypeField di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/python2Packages/delete | Elimina un pacchetto di Automazione di Azure Python 2 |
> | Azione | Microsoft.Automation/automationAccounts/python2Packages/read | Ottiene un pacchetto di Automazione di Azure Python 2 |
> | Azione | Microsoft.Automation/automationAccounts/python2Packages/write | Crea o aggiorna un pacchetto di Automazione di Azure Python 2 |
> | Azione | Microsoft.Automation/automationAccounts/python3Packages/delete | Elimina un pacchetto di Automazione di Azure Python 3 |
> | Azione | Microsoft.Automation/automationAccounts/python3Packages/read | Ottiene un pacchetto di Automazione di Azure Python 3 |
> | Azione | Microsoft.Automation/automationAccounts/python3Packages/write | Crea o aggiorna un pacchetto di Automazione di Azure Python 3 |
> | Azione | Microsoft.Automation/automationAccounts/read | Ottiene un account di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/content/read | Ottiene il contenuto di un runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/delete | Elimina un runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Crea il contenuto di una bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Recupera i risultati dell'operazione della bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/read | Ottiene una bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Ottiene un processo di test per la bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Riprende un processo di test per la bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Interrompe un processo di test per la bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Sospende un processo di test per la bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Crea un processo di test per la bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Annulla le modifiche di una bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Recupera il numero di runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/publish/action | Pubblica una bozza del runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/read | Ottiene un runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/runbooks/write | Crea o aggiorna un runbook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/schedules/delete | Elimina un asset della pianificazione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/schedules/getCount/action | Recupera il numero di pianificazioni di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/schedules/read | Ottiene un asset della pianificazione di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/schedules/write | Crea o aggiorna un asset della pianificazione di automazione di Azure |
> | Azione | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/Read | Ottiene un'esecuzione del computer di configurazione degli aggiornamenti software di automazione di Azure |
> | Azione | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/Read | Ottiene un'esecuzione di configurazione degli aggiornamenti software di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Elimina una configurazione di aggiornamento software di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Elimina una configurazione di aggiornamento software di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Recupera una configurazione di aggiornamento software di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Recupera una configurazione di aggiornamento software di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Crea o aggiorna una configurazione di aggiornamento software di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Crea o aggiorna una configurazione di aggiornamento software di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/statistics/read | Ottiene statistiche di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Recupera un computer di distribuzione per l'aggiornamento di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Recupera un processo di patch per la gestione degli aggiornamenti di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/usages/read | Ottiene l'utilizzo di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/variables/delete | Elimina un asset della variabile di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/variables/read | Esegue la lettura di un asset della variabile di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/variables/write | Crea o aggiorna un asset della variabile di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/delete | Elimina un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/read | Recupera un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/start/action | Avvia un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/stop/action | Arresta un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/streams/read | Ottiene il flusso del processo di un watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Elimina le azioni di un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Recupera le azioni di un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Crea le azioni di un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/watchers/write | Crea un processo watcher di Automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/webhooks/action | Genera un URI per un webhook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/webhooks/delete | Elimina un webhook di automazione di Azure  |
> | Azione | Microsoft.Automation/automationAccounts/webhooks/read | Esegue la lettura un webhook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/webhooks/write | Crea o aggiorna un webhook di automazione di Azure |
> | Azione | Microsoft.Automation/automationAccounts/write | Crea o aggiorna un account di automazione di Azure |
> | Azione | Microsoft.Automation/operations/read | Recupera le operazioni disponibili per le risorse di Automazione di Azure |
> | Azione | Microsoft.Automation/register/action | Registra la sottoscrizione ad Automazione di Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Elimina la risorsa directory B2C |
> | Azione | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visualizza la risorsa directory B2C |
> | Azione | Microsoft.AzureActiveDirectory/b2cDirectories/write | Crea o aggiorna una risorsa della directory B2C |
> | Azione | Microsoft.AzureActiveDirectory/b2ctenants/read | Elenca tutti i tenant B2C in cui l'utente è membro |
> | Azione | Microsoft.AzureActiveDirectory/operations/read | Legge tutte le operazioni API disponibili per il provider di risorse Microsoft.AzureActiveDirectory |
> | Azione | Microsoft.AzureActiveDirectory/register/action | Registra la sottoscrizione per il provider di risorse Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.AzureStack/Operations/read | Ottiene le proprietà di un'operazione del provider di risorse |
> | Azione | Microsoft.AzureStack/register/action | Registra la sottoscrizione con il provider di risorse Microsoft.AzureStack |
> | Azione | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Elimina una sottoscrizione cliente di Azure Stack |
> | Azione | Microsoft.AzureStack/registrations/customerSubscriptions/read | Ottiene le proprietà di una sottoscrizione cliente di Azure Stack |
> | Azione | Microsoft.AzureStack/registrations/customerSubscriptions/write | Crea o aggiorna una sottoscrizione cliente di Azure Stack |
> | Azione | Microsoft.AzureStack/registrations/delete | Elimina una registrazione di Azure Stack |
> | Azione | Microsoft.AzureStack/registrations/getActivationKey/action | Ottiene la chiave di attivazione di Azure Stack più recente |
> | Azione | Microsoft. AzureStack/registrations/Products/GetProduct/Action | Recupera Azure Stack prodotto Marketplace |
> | Azione | Microsoft. AzureStack/registrations/Products/GetProducts/Action | Recupera un elenco di prodotti Azure Stack Marketplace |
> | Azione | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera i dettagli completi per un prodotto Azure Stack Marketplace |
> | Azione | Microsoft.AzureStack/registrations/products/read | Ottiene le proprietà di un prodotto Azure Stack Marketplace |
> | Azione | Microsoft. AzureStack/registrations/Products/uploadProductLog/Action | Registrare lo stato e il timestamp dell'operazione prodotto del Marketplace Azure Stack |
> | Azione | Microsoft.AzureStack/registrations/read | Ottiene le proprietà di una registrazione di Azure Stack |
> | Azione | Microsoft.AzureStack/registrations/write | Crea o aggiorna una registrazione di Azure Stack |
> | Azione | Microsoft. AzureStack/verificationKeys/getCurrentKey/Action | Ottiene la versione corrente di Azure Stack chiave pubblica di firma |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Batch/batchAccounts/applications/delete | Elimina un'applicazione |
> | Azione | Microsoft.Batch/batchAccounts/applications/read | Elenca le applicazioni o ottiene le proprietà di un'applicazione |
> | Azione | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Attiva un pacchetto dell'applicazione |
> | Azione | Microsoft.Batch/batchAccounts/applications/versions/delete | Elimina un pacchetto dell'applicazione |
> | Azione | Microsoft.Batch/batchAccounts/applications/versions/read | Ottiene le proprietà di un pacchetto dell'applicazione |
> | Azione | Microsoft.Batch/batchAccounts/applications/versions/write | Crea un nuovo pacchetto dell'applicazione o ne aggiorna uno esistente |
> | Azione | Microsoft.Batch/batchAccounts/applications/write | Crea una nuova applicazione o ne aggiorna una esistente |
> | Azione | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Ottiene i risultati di un'operazione di certificato a esecuzione prolungata in un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Annulla l'eliminazione non riuscita di un certificato in un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/certificates/delete | Elimina un certificato da un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/certificates/read | Elenca i certificati di un account Batch o ottiene le proprietà di un certificato |
> | Azione | Microsoft.Batch/batchAccounts/certificates/write | Crea un nuovo certificato in un account Batch o aggiorna un certificato esistente |
> | Azione | Microsoft.Batch/batchAccounts/delete | Elimina un account Batch |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Delete | Elimina un processo da un account batch |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Read | Elenca i processi in un account batch o ottiene le proprietà di un processo |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/scrittura | Crea un nuovo processo in un account batch o aggiorna un processo esistente |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Elimina la pianificazione di un processo da un account batch |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Read | Elenca le pianificazioni dei processi in un account batch o ottiene le proprietà di una pianificazione del processo |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Write | Crea una nuova pianificazione del processo in un account batch o aggiorna una pianificazione del processo esistente |
> | Azione | Microsoft.Batch/batchAccounts/listkeys/action | Elenca le chiavi di accesso per un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/operationResults/read | Ottiene i risultati di un'operazione a esecuzione prolungata in un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/poolOperationResults/read | Ottiene i risultati di un'operazione di pool a esecuzione prolungata in un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/pools/delete | Elimina un pool da un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Disabilita il ridimensionamento automatico per un pool di account Batch |
> | Azione | Microsoft.Batch/batchAccounts/pools/read | Elenca i pool in un account Batch o ottiene le proprietà di un pool |
> | Azione | Microsoft.Batch/batchAccounts/pools/stopResize/action | Arresta un'operazione di ridimensionamento in corso in un pool di account Batch |
> | Azione | Microsoft.Batch/batchAccounts/pools/write | Crea un nuovo pool in un account Batch o aggiorna un pool esistente |
> | Azione | Microsoft.Batch/batchAccounts/read | Elenca account Batch oppure ottiene le proprietà di un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/regeneratekeys/action | Rigenera le chiavi di accesso per un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Consente di sincronizzare le chiavi di accesso per l'account di archiviazione automatica configurato per un account Batch |
> | Azione | Microsoft.Batch/batchAccounts/write | Crea un nuovo account Batch o aggiorna un account Batch esistente |
> | Azione | Microsoft.Batch/locations/accountOperationResults/read | Ottiene i risultati di un'operazione a esecuzione prolungata in un account Batch |
> | Azione | Microsoft.Batch/locations/checkNameAvailability/action | Controlla che il nome di account sia valido e non in uso. |
> | Azione | Microsoft.Batch/locations/quotas/read | Ottiene quote Batch della sottoscrizione specificata nell'area di Azure specificata |
> | Azione | Microsoft.Batch/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.Batch |
> | Azione | Microsoft.Batch/register/action | Registra la sottoscrizione per il provider di risorse Batch e consente la creazione di account Batch |
> | Azione | Microsoft.Batch/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse Batch e impedisce la creazione di account Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. Billing/billingAccounts/Agreements/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/billingPermissions/lettura |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/billingPermissions/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/Customers/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/fatture/pricesheets/download/azione |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Move/Action |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Transfer/Action |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/Action |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/Move/Action |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/Transfer/Action |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/pricesheets/download/azione |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/lettura |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/Write |  |
> | Azione | Microsoft. Billing/billingAccounts/billingProfiles/Write |  |
> | Azione | Microsoft. Billing/billingAccounts/billingSubscriptions/lettura |  |
> | Azione | Microsoft. Billing/billingAccounts/Customers/billingPermissions/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/Customers/Read |  |
> | Azione | Microsoft.Billing/billingAccounts/departments/read |  |
> | Azione | Microsoft. Billing/billingAccounts/enrollmentAccounts/billingPermissions/Read |  |
> | Azione | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Azione | Microsoft. Billing/billingAccounts/enrollmentDepartments/billingPermissions/Read |  |
> | Azione | Microsoft. Billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/azione |  |
> | Azione | Microsoft. Billing/billingAccounts/Products/Read |  |
> | Azione | Microsoft.Billing/billingAccounts/read |  |
> | Azione | Microsoft. Billing/billingAccounts/Write |  |
> | Azione | Microsoft.Billing/departments/read |  |
> | Azione | Microsoft. Billing/fatture/download/azione | Scarica la fattura usando il collegamento per il download dall'elenco |
> | Azione | Microsoft.Billing/invoices/read |  |
> | Azione | Microsoft.Billing/register/action |  |
> | Azione | Microsoft. Billing/validateAddress/azione |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.BingMaps/mapApis/Delete | Operazione di eliminazione |
> | Azione | Microsoft.BingMaps/mapApis/listSecrets/action | Elenca i segreti |
> | Azione | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Esegue la lettura del token di autorizzazione Single Sign-On per la risorsa |
> | Azione | Microsoft.BingMaps/mapApis/Read | Operazione di lettura |
> | Azione | Microsoft.BingMaps/mapApis/regenerateKey/action | Rigenera la chiave |
> | Azione | Microsoft.BingMaps/mapApis/Write | Operazione di scrittura |
> | Azione | Microsoft.BingMaps/Operations/read | Descrizione dell'operazione. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Blockchain/blockchainMembers/delete | Elimina un membro blockchain esistente. |
> | Azione | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Ottiene o elenca le chiavi API membro blockchain esistenti. |
> | Azione | Microsoft.Blockchain/blockchainMembers/read | Ottiene o elenca i membri blockchain esistenti. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Stabilisce la connessione a un nodo di transazione membro blockchain. |
> | Azione | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Elimina un nodo di transazione membro blockchain esistente. |
> | Azione | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Ottiene o elenca le chiavi API del nodo di transazione del membro blockchain esistente. |
> | Azione | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Ottiene o elenca i nodi di transazione del membro blockchain esistenti. |
> | Azione | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Crea o aggiorna un nodo di transazione membro blockchain. |
> | Azione | Microsoft.Blockchain/blockchainMembers/write | Crea o aggiorna un membro blockchain. |
> | Azione | Microsoft. blockchain/cordaMembers/Delete | Elimina un membro blockchain corda esistente. |
> | Azione | Microsoft. blockchain/cordaMembers/Read | Ottiene o elenca i membri blockchain corda esistenti. |
> | Azione | Microsoft. blockchain/cordaMembers/Write | Crea o aggiorna un membro blockchain corda. |
> | Azione | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Ottiene i risultati dell'operazione dei membri blockchain. |
> | Azione | Microsoft.Blockchain/locations/checkNameAvailability/action | Verifica che il nome della risorsa sia valido e non sia in uso. |
> | Azione | Microsoft. blockchain/Operations/Read | Elencare tutte le operazioni nel provider di risorse Microsoft blockchain. |
> | Azione | Microsoft. blockchain/Register/Action | Registra la sottoscrizione per il provider di risorse blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Esegue la lettura degli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprintAssignments/delete | Eliminare gli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprintAssignments/read | Esegue la lettura degli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Ottiene l'ID oggetto entità servizio dell'entità servizio di Azure Blueprints. |
> | Azione | Microsoft.Blueprint/blueprintAssignments/write | Creare o aggiornare gli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprints/artifacts/delete | Eliminare gli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprints/artifacts/read | Esegue la lettura degli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprints/artifacts/write | Creare o aggiornare gli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprints/delete | Elimina i progetti |
> | Azione | Microsoft.Blueprint/blueprints/read | Esegue la lettura dei progetti |
> | Azione | Microsoft.Blueprint/blueprints/versions/artifacts/read | Esegue la lettura degli artefatti del progetto |
> | Azione | Microsoft.Blueprint/blueprints/versions/delete | Elimina i progetti |
> | Azione | Microsoft.Blueprint/blueprints/versions/read | Esegue la lettura dei progetti |
> | Azione | Microsoft.Blueprint/blueprints/versions/write | Crea o aggiorna i progetti |
> | Azione | Microsoft.Blueprint/blueprints/write | Crea o aggiorna i progetti |
> | Azione | Microsoft.Blueprint/register/action | Registra il provider di risorse di progetto Azure |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.BotService/botServices/channels/delete | Elimina un servizio bot |
> | Azione | Microsoft.BotService/botServices/channels/read | Legge un servizio bot |
> | Azione | Microsoft.BotService/botServices/channels/write | Scrive un servizio bot |
> | Azione | Microsoft.BotService/botServices/connections/delete | Elimina un servizio bot |
> | Azione | Microsoft.BotService/botServices/connections/read | Legge un servizio bot |
> | Azione | Microsoft.BotService/botServices/connections/write | Scrive un servizio bot |
> | Azione | Microsoft.BotService/botServices/delete | Elimina un servizio bot |
> | Azione | Microsoft.BotService/botServices/read | Legge un servizio bot |
> | Azione | Microsoft.BotService/botServices/write | Scrive un servizio bot |
> | Azione | Microsoft.BotService/locations/operationresults/read | Legge lo stato di un'operazione asincrona |
> | Azione | Microsoft.BotService/Operations/read | Legge le operazioni per tutti i tipi di risorsa |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Cache/checknameavailability/action | Verifica se un nome è disponibile per essere assegnato a nuova cache Redis |
> | Azione | Microsoft.Cache/locations/operationresults/read | Ottiene il risultato di un'operazione a esecuzione prolungata per la quale l'intestazione "Location" è stata restituita precedentemente al client |
> | Azione | Microsoft.Cache/operations/read | Elenca le operazioni supportate dal provider "Microsoft.Cache". |
> | Azione | Microsoft.Cache/redis/delete | Elimina l'intera Cache Redis |
> | Azione | Microsoft.Cache/redis/export/action | Esporta i dati Redis nei BLOB di archiviazione predefiniti nel formato specificato |
> | Azione | Microsoft.Cache/redis/firewallRules/delete | Elimina le regole del firewall IP di una cache Redis |
> | Azione | Microsoft.Cache/redis/firewallRules/read | Ottiene le regole del firewall IP di una cache Redis |
> | Azione | Microsoft.Cache/redis/firewallRules/write | Modifica le regole del firewall IP di una cache Redis |
> | Azione | Microsoft.Cache/redis/forceReboot/action | Forza il riavvio di un'istanza della cache, con possibile perdita di dati. |
> | Azione | Microsoft.Cache/redis/import/action | Importa i dati di un formato specificato da più BLOB in Redis |
> | Azione | Microsoft.Cache/redis/linkedservers/delete | Elimina un server collegato da una cache Redis |
> | Azione | Microsoft.Cache/redis/linkedservers/read | Ottiene i server collegati associati a una cache Redis. |
> | Azione | Microsoft.Cache/redis/linkedservers/write | Aggiunge un server collegato a una cache Redis |
> | Azione | Microsoft.Cache/redis/listKeys/action | Visualizza il valore delle chiavi di accesso della Cache Redis nel portale di gestione |
> | Azione | Microsoft.Cache/redis/listUpgradeNotifications/read | Elenca le notifiche di aggiornamento più recenti per il tenant della cache. |
> | Azione | Microsoft.Cache/redis/metricDefinitions/read | Ottiene la metrica disponibile per una cache Redis |
> | Azione | Microsoft.Cache/redis/patchSchedules/delete | Elimina la pianificazione dell'applicazione di patch di una cache Redis |
> | Azione | Microsoft.Cache/redis/patchSchedules/read | Ottiene la pianificazione dell'applicazione di patch di una cache Redis |
> | Azione | Microsoft.Cache/redis/patchSchedules/write | Modifica la pianificazione dell'applicazione di patch di una cache Redis |
> | Azione | Microsoft.Cache/redis/read | Visualizza la configurazione e le impostazioni della Cache Redis nel portale di gestione |
> | Azione | Microsoft.Cache/redis/regenerateKey/action | Modifica il valore delle chiavi di accesso della Cache Redis nel portale di gestione |
> | Azione | Microsoft.Cache/redis/start/action | Avvia un'istanza della cache. |
> | Azione | Microsoft.Cache/redis/stop/action | Arresta un'istanza della cache. |
> | Azione | Microsoft.Cache/redis/write | Modifica la configurazione e le impostazioni della Cache Redis nel portale di gestione |
> | Azione | Microsoft.Cache/register/action | Registra il provider di risorse 'Microsoft.Cache' con una sottoscrizione |
> | Azione | Microsoft.Cache/unregister/action | Annulla la registrazione del provider di risorse 'Microsoft.Cache' con una sottoscrizione |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Capacity/appliedreservations/read | Legge tutte le prenotazioni |
> | Azione | Microsoft.Capacity/calculateexchange/action | Calcola la quantità di scambio e il prezzo del nuovo acquisto e restituisce gli errori dei criteri. |
> | Azione | Microsoft.Capacity/calculateprice/action | Calcola qualsiasi prezzo di prenotazione |
> | Azione | Microsoft.Capacity/catalogs/read | Legge il catalogo delle prenotazioni |
> | Azione | Microsoft.Capacity/checkoffers/action | Controlla eventuali offerte di sottoscrizione |
> | Azione | Microsoft.Capacity/checkscopes/action | Controlla tutte le sottoscrizioni |
> | Azione | Microsoft.Capacity/commercialreservationorders/read | Ottiene gli ordini di prenotazione creati in qualsiasi tenant |
> | Azione | Microsoft.Capacity/exchange/action | Scambia qualsiasi prenotazione |
> | Azione | Microsoft.Capacity/operations/read | Legge qualsiasi operazione |
> | Azione | Microsoft.Capacity/register/action | Registra il provider di risorse Capacity e consente la creazione di risorse Capacity. |
> | Azione | Microsoft.Capacity/reservationorders/action | Aggiorna tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/availablescopes/action | Trova gli ambiti disponibili |
> | Azione | Microsoft.Capacity/reservationorders/calculaterefund/action | Calcola l'importo del rimborso e il prezzo del nuovo acquisto e restituisce gli errori dei criteri. |
> | Azione | Microsoft.Capacity/reservationorders/delete | Elimina tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/merge/action | Unisce tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/read | Legge tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/reservations/action | Aggiorna tutte le prenotazioni |
> | Azione | Microsoft. Capacity/reservationorders/prenotazioni/availablescopes/azione | Trova gli ambiti disponibili |
> | Azione | Microsoft.Capacity/reservationorders/reservations/delete | Elimina tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/reservations/read | Legge tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/reservations/revisions/read | Legge tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/reservations/write | Crea tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/return/action | Restituisce tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/split/action | Divide tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/swap/action | Scambia tutte le prenotazioni |
> | Azione | Microsoft.Capacity/reservationorders/write | Crea tutte le prenotazioni |
> | Azione | Microsoft.Capacity/tenants/register/action | Registra tutti i tenant |
> | Azione | Microsoft.Capacity/unregister/action | Annullare la registrazione di tutti i tenant |
> | Azione | Microsoft.Capacity/validatereservationorder/action | Convalida tutte le prenotazioni |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Delete |  |
> | Azione | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Read |  |
> | Azione | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Write |  |
> | Azione | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Delete |  |
> | Azione | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Read |  |
> | Azione | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Write |  |
> | Azione | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Azione | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Azione | Microsoft.Cdn/edgenodes/delete |  |
> | Azione | Microsoft.Cdn/edgenodes/read |  |
> | Azione | Microsoft.Cdn/edgenodes/write |  |
> | Azione | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Delete |  |
> | Azione | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Read |  |
> | Azione | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Write |  |
> | Azione | Microsoft.Cdn/operationresults/delete |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Azione | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Azione | Microsoft.Cdn/operationresults/read |  |
> | Azione | Microsoft.Cdn/operationresults/write |  |
> | Azione | Microsoft.Cdn/operations/read |  |
> | Azione | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Azione | Microsoft.Cdn/profiles/delete |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/read |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Azione | Microsoft.Cdn/profiles/endpoints/write |  |
> | Azione | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Azione | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Azione | Microsoft.Cdn/profiles/read |  |
> | Azione | Microsoft.Cdn/profiles/write |  |
> | Azione | Microsoft.Cdn/register/action | Registra la sottoscrizione per il provider di risorse della rete CDN e consente la creazione di profili CDN. |
> | Azione | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Elimina un certificato esistente |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Ottiene l'elenco dei certificati |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Aggiunge un nuovo certificato o ne aggiorna uno esistente |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/Delete | Elimina un certificato del servizio app esistente |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/Read | Ottiene l'elenco degli ordini di certificato |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Riemette un ordine di certificato esistente |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Rinnova un ordine di certificato esistente |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Invia di nuovo la posta elettronica di certificato |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Invia di nuovo messaggi di richiesta a un altro indirizzo di posta elettronica |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recupera il sealing di sito per un certificato del servizio app emesso |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recupera l'elenco di azioni di certificato |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recupera la cronologia della posta elettronica di certificato |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificare la proprietà del dominio |
> | Azione | Microsoft.CertificateRegistration/certificateOrders/Write | Aggiunge un nuovo ordine di certificato o ne aggiorna uno esistente |
> | Azione | Microsoft.CertificateRegistration/operations/Read | Elenca tutte le operazioni dalla registrazione del certificato di servizio dell'app |
> | Azione | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Esegue il provisioning dell'entità servizio per l'entità app del servizio |
> | Azione | Microsoft.CertificateRegistration/register/action | Registra il provider di risorse certificati Microsoft per la sottoscrizione |
> | Azione | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Convalida l'oggetto acquisto di certificato senza inviarlo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ClassicCompute/capabilities/read | Mostra le funzionalità |
> | Azione | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Controlla la disponibilità di un nome di dominio specifico. |
> | Azione | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Recupera la disponibilità di un nome di dominio specificato. |
> | Azione | Microsoft.ClassicCompute/domainNames/active/write | Imposta il nome del dominio attivo. |
> | Azione | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Visualizza il set di disponibilità per la risorsa. |
> | Azione | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra le funzionalità del nome di dominio |
> | Azione | Microsoft.ClassicCompute/domainNames/delete | Rimuove i nomi di dominio per le risorse. |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra gli slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Ottiene il ruolo per lo slot di distribuzione del nome di dominio |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Ottiene l'istanza del ruolo per il ruolo per lo slot di distribuzione del nome di dominio |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Recupera lo stato dello slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Aggiunge lo stato dello slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Recupera il dominio di aggiornamento per lo slot di distribuzione nel nome di dominio |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Aggiorna il dominio di aggiornamento per lo slot di distribuzione nel nome di dominio |
> | Azione | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Crea o aggiorna la distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/extensions/delete | Rimuove le estensioni del nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Esegue la lettura dello stato dell'operazione per le estensioni dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/extensions/read | Restituisce le estensioni del nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/extensions/write | Aggiunge le estensioni del nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Rimuove un nuovo bilanciamento del carico interno. |
> | Azione | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Legge lo stato dell'operazione per i servizi di bilanciamento del carico dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Ottiene i servizi di bilanciamento del carico interno. |
> | Azione | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Crea un nuovo bilanciamento del carico interno. |
> | Azione | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Legge lo stato dell'operazione per gli endpoint con bilanciamento del carico per i nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Recupera i set di endpoint con carico bilanciato. |
> | Azione | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Aggiunge i set di endpoint con carico bilanciato. |
> | Azione | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Recupera lo stato dell'operazione del nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Esegue la lettura dello stato dell'operazione per le estensioni dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/read | Restituisce i nomi di dominio per le risorse. |
> | Azione | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Elimina i certificati di servizio usati. |
> | Azione | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Esegue la lettura dello stato dell'operazione per i certificati di servizio dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Restituisce i certificati di servizio usati. |
> | Azione | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Aggiunge o modifica i certificati di servizio usati. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Interrompe la migrazione di uno slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Esegue il commit della migrazione di uno slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/delete | Elimina uno slot di distribuzione specifico. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Esegue la lettura dello stato dell'operazione per gli slot dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara la migrazione di uno slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/read | Mostra gli slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Rimuove il riferimento all'estensione del ruolo dello slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Legge lo stato operazione dei riferimenti all'estensione per i ruoli slot dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Restituisce il riferimento all'estensione del ruolo dello slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Aggiunge o modifica il riferimento all'estensione del ruolo dello slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Recupera la definizione della metrica del ruolo per il nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Recupera la metrica del ruolo per il nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Recupera lo stato dell'operazione per il ruolo slot dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/read | Ottiene il ruolo per lo slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Scarica il file di connessione Desktop remoto per l'istanza di ruolo nel ruolo slot del nome di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Recupera lo stato dell'operazione per l'istanza del ruolo nel ruolo slot dei nomi di dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Ottiene l'istanza del ruolo. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Ricostruisce l'istanza del ruolo. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Ricrea l'immagine dell'istanza del ruolo. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Riavvia le istanze del ruolo. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Recupera lo SKU del ruolo per lo slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/roles/write | Aggiunge il ruolo per lo slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/start/action | Avvia uno slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Imposta lo stato dello slot di distribuzione su arrestato. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Imposta lo stato dello slot di distribuzione su avviato. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/stop/action | Sospende lo slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Esegue l'aggiornamento del dominio. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Convalida la migrazione di uno slot di distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/slots/write | Crea o aggiorna la distribuzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/swap/action | Passa dallo slot di staging allo slot di produzione. |
> | Azione | Microsoft.ClassicCompute/domainNames/write | Aggiunge o modifica i nomi di dominio per le risorse. |
> | Azione | Microsoft.ClassicCompute/moveSubscriptionResources/action | Sposta tutte le risorse classiche in un'altra sottoscrizione. |
> | Azione | Microsoft.ClassicCompute/operatingSystemFamilies/read | Elenca le famiglie di sistemi operativi guest disponibili in Microsoft Azure ed elenca anche le versioni dei sistemi operativi disponibili per ogni famiglia. |
> | Azione | Microsoft.ClassicCompute/operatingSystems/read | Elenca le versioni dei sistemi operativi guest attualmente disponibili in Microsoft Azure. |
> | Azione | Microsoft.ClassicCompute/operations/read | Recupera l'elenco delle operazioni. |
> | Azione | Microsoft.ClassicCompute/operationStatuses/read | Legge lo stato dell'operazione per la risorsa. |
> | Azione | Microsoft.ClassicCompute/quotas/read | Ottiene la quota per la sottoscrizione. |
> | Azione | Microsoft.ClassicCompute/register/action | Consente di eseguire la registrazione per Calcolo classico |
> | Azione | Microsoft.ClassicCompute/resourceTypes/skus/read | Ottiene l'elenco degli SKU per i tipi di risorse supportati. |
> | Azione | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Convalida la disponibilità della sottoscrizione per l'operazione di spostamento classica. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Elimina il gruppo di sicurezza di rete associato alla macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Legge lo stato operazione dei gruppi di sicurezza di rete associati alle macchine virtuali. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Ottiene il gruppo di sicurezza di rete associato alla macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Aggiunge un gruppo di sicurezza di rete associato alla macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Ottiene le operazioni asincrone possibili |
> | Azione | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Collega un disco dati a una macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/capture/action | Acquisisce una macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/delete | Rimuove le macchine virtuali. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Scollega un disco dati da una macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Recupera le impostazioni di diagnostica della macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/disks/read | Recupera l'elenco dei dischi dati |
> | Azione | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Scarica il file RDP per la macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Legge lo stato operazione delle estensioni macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/extensions/read | Ottiene l'estensione macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/extensions/write | Visualizza l'estensione macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Recupera la definizione delle metriche della macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/metrics/read | Ottiene la metrica. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Elimina il gruppo di sicurezza di rete associato all'interfaccia di rete. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Legge lo stato operazione dei gruppi di sicurezza di rete associati alle macchine virtuali. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Ottiene il gruppo di sicurezza di rete associato all'interfaccia di rete. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Aggiunge un gruppo di sicurezza di rete associato all'interfaccia di rete. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Legge lo stato operazione delle macchine virtuali. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Esegue operazioni di manutenzione sulla macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/read | Recupera l'elenco delle macchine virtuali. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Ridistribuisce la macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/restart/action | Riavvia le macchine virtuali. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Chiude la macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/start/action | Avviare la macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/stop/action | Arresta la macchina virtuale. |
> | Azione | Microsoft.ClassicCompute/virtualMachines/write | Aggiunge o modifica macchine virtuali. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Recupera lo stato operativo della Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Elimina il peering della Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Recupera lo stato dell'operazione di peering della Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Recupera il peering della Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Aggiunge il peering della Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Recupera le Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Aggiunge le Cross Connection ExpressRoute. |
> | Azione | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Recupera l'elenco dei dispositivi supportati. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Elimina il gruppo di sicurezza di rete. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Legge lo stato operazione del gruppo di sicurezza di rete. |
> | Azione | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene le impostazioni di diagnostica dei gruppi di sicurezza di rete |
> | Azione | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna le impostazioni di diagnostica dei gruppi di sicurezza di rete. L'operazione viene eseguita dal provider di risorse Insights. |
> | Azione | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Ottiene gli eventi per un gruppo di sicurezza di rete |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/read | Ottiene il gruppo di sicurezza di rete. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Elimina la regola di sicurezza. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Legge lo stato dell'operazione per le regole di sicurezza del gruppo di sicurezza di rete. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Ottiene la regola di sicurezza. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Aggiunge o aggiorna una regola di sicurezza. |
> | Azione | Microsoft.ClassicNetwork/networkSecurityGroups/write | Aggiunge un nuovo gruppo di sicurezza di rete. |
> | Azione | Microsoft.ClassicNetwork/operations/read | Recupera le operazioni della rete classica. |
> | Azione | Microsoft.ClassicNetwork/quotas/read | Ottiene la quota per la sottoscrizione. |
> | Azione | Microsoft.ClassicNetwork/register/action | Esegue la registrazione per Rete classica |
> | Azione | Microsoft.ClassicNetwork/reservedIps/delete | Elimina un IP riservato. |
> | Azione | Microsoft.ClassicNetwork/reservedIps/join/action | Unisce un IP riservato |
> | Azione | Microsoft.ClassicNetwork/reservedIps/link/action | Collega un IP riservato |
> | Azione | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Legge lo stato dell'operazione per gli IP riservati. |
> | Azione | Microsoft.ClassicNetwork/reservedIps/read | Ottiene gli IP riservati |
> | Azione | Microsoft.ClassicNetwork/reservedIps/write | Aggiunge un nuovo IP riservato |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Interrompe la migrazione di una rete virtuale |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra le funzionalità |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verifica la disponibilità di un determinato indirizzo IP in una rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Esegue il commit della migrazione di una rete virtuale |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/delete | Elimina la rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Annulla la revoca di un certificato client. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Esegue la lettura dei certificati client revocati. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoca un certificato client. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Elimina il certificato client del gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Scarica il certificato in base all'identificazione personale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Elenca il pacchetto del certificato del gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Trova i certificati radice client. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carica un nuovo certificato radice client. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Connette una connessione gateway da sito a sito. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Disconnette una connessione gateway da sito a sito. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Recupera l'elenco di connessioni. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Esegue il test di una connessione gateway da sito a sito. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Elimina il gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Scarica lo script di configurazione del dispositivo. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Scarica la diagnostica del gateway. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Recupera la chiave del servizio del circuito. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Elenca i pacchetti del gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Legge lo stato operazione dei gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Ottiene il pacchetto del gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Ottiene i gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Avvia la diagnostica per il gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Arresta la diagnostica per il gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Aggiunge un gateway di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/join/action | Unisce la rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Legge lo stato operazione delle reti virtuali. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Associa una rete virtuale a un'altra rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara la migrazione di una rete virtuale |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/read | Ottiene la rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Elimina il proxy del peering di rete virtuale remoto. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Ottiene il proxy del peering di rete virtuale remoto. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Aggiunge o aggiorna il proxy del peering di rete virtuale remoto. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Elimina il gruppo di sicurezza di rete associato alla subnet. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Legge lo stato operazione del gruppo di sicurezza di rete associato alla subnet della rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Ottiene il gruppo di sicurezza di rete associato alla subnet. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Aggiunge un gruppo di sicurezza di rete associato alla subnet. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Convalida la migrazione di una rete virtuale |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Ottiene il peering di rete virtuale. |
> | Azione | Microsoft.ClassicNetwork/virtualNetworks/write | Aggiunge una nuova rete virtuale. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ClassicStorage/capabilities/read | Mostra le funzionalità |
> | Azione | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verifica la disponibilità di un account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Recupera la disponibilità di un account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/disks/read | Restituisce il disco dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/images/operationstatuses/read | Recupera lo stato operativo dell'immagine. |
> | Azione | Microsoft.ClassicStorage/images/read | Restituisce l'immagine. |
> | Azione | Microsoft.ClassicStorage/operations/read | Recupera le operazioni della risorsa di archiviazione classica |
> | Azione | Microsoft.ClassicStorage/osImages/read | Restituisce l'immagine del sistema operativo. |
> | Azione | Microsoft.ClassicStorage/osPlatformImages/read | Ottiene l'immagine della piattaforma del sistema operativo. |
> | Azione | Microsoft.ClassicStorage/publicImages/read | Ottiene l'immagine di macchina virtuale pubblica. |
> | Azione | Microsoft.ClassicStorage/quotas/read | Ottiene la quota per la sottoscrizione. |
> | Azione | Microsoft.ClassicStorage/register/action | Consente di eseguire la registrazione per Archiviazione (versione classica) |
> | Azione | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Interrompe la migrazione di un account di archiviazione. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/blobServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Esegue il commit della migrazione di un account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/delete | Elimina l'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/disks/delete | Elimina un disco specifico dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Legge lo stato dell'operazione per la risorsa. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/disks/read | Restituisce il disco dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/disks/write | Aggiunge un disco dell'account di archiviazione. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. Insights/diagnosticSettings/Write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. Insights/metricDefinitions/Read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/images/delete | Elimina una data immagine dell'account di archiviazione. Operazione deprecata: usare 'Microsoft.ClassicStorage/storageAccounts/vmImages'. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Restituisce lo stato operativo dell'immagine dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/images/read | Restituisce l'immagine dell'account di archiviazione. Operazione deprecata: usare 'Microsoft.ClassicStorage/storageAccounts/vmImages'. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Legge lo stato dell'operazione per la risorsa. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Elimina un'immagine specifica del sistema operativo dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/osImages/read | Restituisce l'immagine del sistema operativo dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/osImages/write | Aggiunge un'immagine specifica del sistema operativo dell'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara la migrazione di un account di archiviazione. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/queueServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Rigenera le chiavi di accesso esistenti per l'account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Ottiene la metrica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/services/read | Ottiene i servizi disponibili. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/diagnosticSettings/Write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft. ClassicStorage/storageAccounts/tableServices/Providers/Microsoft. Insights/metricDefinitions/Read | Ottiene le definizioni di metrica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Convalida la migrazione di un account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Elimina un'immagine di macchina virtuale specifica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Recupera lo stato operativo dell'immagine di macchina virtuale specifica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Restituisce l'immagine di macchina virtuale. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Aggiunge un'immagine di macchina virtuale specifica. |
> | Azione | Microsoft.ClassicStorage/storageAccounts/write | Aggiunge un nuovo account di archiviazione. |
> | Azione | Microsoft.ClassicStorage/vmImages/read | Elenca le immagini delle macchine virtuali. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | DataAction | Microsoft. CognitiveServices/accounts/AnomalyDetector/timeseries/Inter/Detect/Action | Questa operazione genera un modello utilizzando un'intera serie. ogni punto viene rilevato con lo stesso modello.<br>Con questo metodo, i punti prima e dopo un determinato punto vengono usati per determinare se si tratta di un'anomalia.<br>L'intero rilevamento può fornire all'utente lo stato complessivo della serie temporale. |
> | DataAction | Microsoft. CognitiveServices/accounts/AnomalyDetector/timeseries/Last/Detect/Action | Questa operazione genera un modello usando i punti precedenti a quello più recente. Con questo metodo, vengono usati solo i punti cronologici per determinare se il punto di destinazione è un'anomalia. Il punto di rilevamento più recente corrisponde allo scenario di monitoraggio in tempo reale delle metriche aziendali. |
> | DataAction | Microsoft. CognitiveServices/accounts/suggerimenti automatici/ricerca/azione | Questa operazione fornisce suggerimenti per una query o una query parziale specificata. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Questa operazione estrae un set completo di funzionalità visive basate sul contenuto di un'immagine.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Questa operazione restituisce un rettangolo di delimitazione intorno all'area più importante dell'immagine. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Questa operazione genera una descrizione di un'immagine in lingua leggibile con frasi complete.<br> La descrizione è basata su una raccolta di tag di contenuto, anch ' essa restituiti dall'operazione.<br>È possibile generare più di una descrizione per ogni immagine.<br> Le descrizioni sono ordinate in base al Punteggio di confidenza.<br>Tutte le descrizioni sono in inglese. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Questa operazione esegue il rilevamento di oggetti sull'immagine specificata.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Questa operazione genera un'immagine di anteprima con larghezza e altezza specificate dall'utente.<br> Per impostazione predefinita il servizio analizza l'immagine, identifica l'area di interesse e genera le coordinate di ritaglio intelligente sulla base dell'area di interesse.<br> Il ritaglio intelligente consente di specificare le proporzioni che differiscono da quelle dell'immagine di input |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Questa operazione riconosce il contenuto all'interno di un'immagine applicando un modello specifico di dominio.<br> È possibile recuperare l'elenco dei modelli specifici del dominio supportati dal API Visione artificiale usando la richiesta/Models GET.<br> Attualmente, l'API fornisce i modelli specifici di dominio seguenti: celebrità, punti di riferimento. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Questa operazione restituisce l'elenco dei modelli specifici del dominio supportati dal API Visione artificiale.  Attualmente, l'API supporta i seguenti modelli specifici del dominio: riconoscimento celebrità, riconoscimento di riferimento. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Il riconoscimento ottico dei caratteri (OCR) rileva il testo in un'immagine ed estrae i caratteri riconosciuti in un flusso di caratteri utilizzabile dal computer.    |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Analyze/Action | Usare questa interfaccia per eseguire un'operazione di lettura, usando gli algoritmi di riconoscimento ottico dei caratteri (OCR) all'avanguardia ottimizzati per i documenti con un numero elevato di testo.<br>Può gestire documenti scritti a mano, stampati o misti.<br>Quando si usa l'interfaccia di lettura, la risposta contiene un'intestazione denominata ' Operation-location '.<br>L'intestazione ' Operation-location ' contiene l'URL che è necessario usare per l'operazione Get Read result per accedere ai risultati OCR. |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/analyzeresults/Read | Utilizzare questa interfaccia per recuperare lo stato e il risultato OCR di un'operazione di lettura.  L'URL che contiene ' operationId ' viene restituito nell'intestazione della risposta ' Operation-location ' dell'operazione di lettura. |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/Read/Core/asyncbatchanalyze/Action | Usare questa interfaccia per ottenere il risultato di un'operazione di lettura di un file batch, usando il carattere ottico all'avanguardia |
> | DataAction | Microsoft. CognitiveServices/accounts/ComputerVision/read/operation/Read | Questa interfaccia viene usata per ottenere i risultati OCR dell'operazione di lettura. L'URL di questa interfaccia deve essere recuperato dal campo <b>"Operation-location"</b> restituito dall'interfaccia del file Read di batch. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Usare questa interfaccia per ottenere il risultato di un'operazione di riconoscimento del testo. Quando si usa l'interfaccia riconoscimento del testo, la risposta contiene un campo denominato "Operation-location". Il campo "Operation-location" contiene l'URL che è necessario usare per l'operazione Get riconoscimento del testo Operation Result. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Questa operazione genera un elenco di parole, o tag, rilevanti per il contenuto dell'immagine fornita.<br>Il API Visione artificiale può restituire tag in base a oggetti, esseri viventi, scenari o azioni trovate nelle immagini.<br>Diversamente dalle categorie, i tag non sono organizzati in base a un sistema di classificazione gerarchico, ma corrispondono al contenuto di un'immagine.<br>I tag possono contenere hint per evitare ambiguità o fornire contesto, ad esempio il tag "violoncello" può essere accompagnato dall'hint "Musical Instrument".<br>Tutti i tag sono in inglese. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Questa interfaccia viene utilizzata per ottenere il risultato dell'operazione di riconoscimento del testo. L'URL di questa interfaccia deve essere recuperato dal campo <b>"Operation-location"</b> restituito dall'interfaccia riconoscimento del testo. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/Action | Crea elenco immagini. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/Delete | Elenchi di immagini-Elimina |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/images/Delete | Eliminare un'immagine dall'elenco di immagini. L'elenco di immagini può essere usato per eseguire la corrispondenza fuzzy con altre immagini quando si usa l'API Image/match. Elimina tutte le immagini dall'elenco. L'elenco di immagini può essere usato per eseguire la corrispondenza fuzzy con altre immagini quando si usa l'API Image/match. * |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/images/Read | Image-ottenere tutti gli ID immagine |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/images/Write | Aggiungere un'immagine all'elenco di immagini. L'elenco di immagini può essere usato per eseguire la corrispondenza fuzzy con altre immagini quando si usa l'API Image/match. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/Read | Elenchi di immagini-ottenere i dettagli-elenchi di immagini-Ottieni tutto |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/refreshindex/Action | Elenchi di immagini-Aggiorna indice di ricerca |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/unagels/Write | Elenchi di immagini-Dettagli aggiornamento |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessImage/Evaluate/Action | Restituisce le probabilità dell'immagine che contiene contenuto audace o per adulti. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessImage/findfaces/Action | Trova i visi nelle immagini. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessImage/match/Action | Fuzzily corrisponde a un'immagine per uno degli elenchi di immagini personalizzate.<br>È possibile creare e gestire gli elenchi di immagini personalizzate tramite questa API.<br> |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessImage/OCR/Action | Restituisce qualsiasi testo trovato nell'immagine per la lingua specificata. Se nell'input non è specificata alcuna lingua, per impostazione predefinita il rilevamento viene impostato su inglese. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessText/DetectLanguage/Action | Questa operazione rileverà la lingua del contenuto di input specificato.<br>Restituisce il codice ISO 639-3 per il linguaggio predominante che comprende il testo inviato.<br>Sono supportate più di 110 lingue. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/ProcessText/screen/Action | The operation detects profanity in more than 100 languages and match against custom and shared blacklists. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/Jobs/Action | Verrà restituito un ID processo per il contenuto dell'immagine pubblicato in questo endpoint.  |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/Jobs/Read | Ottenere i dettagli del processo per un ID processo. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisios/AccessKey/Read | Ottenere la chiave di accesso per la verifica del contenuto per il team. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisioni/azione | Le recensioni create verranno visualizzate per i revisori del team. Poiché i revisori completano la revisione, i risultati della revisione verranno pubblicati (ad esempio HTTP POST) per il CallBackEndpoint specificato. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/reviews/frames/Read | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisioni/frame/Write | Utilizzare questo metodo per aggiungere frame per una revisione del video. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisios/Publish/Action | Le verifiche video vengono inizialmente create in uno stato non pubblicato, il che significa che non è ancora disponibile per i revisori del team. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisios/Read | Restituisce i dettagli di revisione per l'ID revisione passato. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisioni/Transcript/Action | Questa API aggiunge un file di trascrizione (la versione del testo di tutte le parole in un video) a una recensione video. Il file deve essere un formato WebVTT valido. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/revisios/transcriptmoderationresult/Action | Questa API aggiunge un file di risultati del testo della trascrizione per una revisione video. Il file dei risultati del testo della trascrizione è il risultato di un'API per il testo della schermata. Per generare il file dei risultati del testo dello schermo della trascrizione, è necessario selezionare un file di trascrizione per la volgarità usando l'API per il testo della schermata. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/Settings/Templates/Delete | Eliminare un modello nel team |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/Settings/templates/Read | Restituisce una matrice di modelli di revisione di cui è stato effettuato il provisioning in questo team. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/Settings/templates/Write | Crea o aggiorna il modello specificato |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/workflows/Read | Ottenere i dettagli di un flusso di lavoro specifico del team. Ottenere tutti i flussi di lavoro disponibili per il team * |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/teams/workflows/Write | Creare un nuovo flusso di lavoro o aggiornarne uno esistente. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/Action | Crea elenco di termini. |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/BulkUpdate/Action | Elenchi di termini-aggiornamento in blocco |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/Delete | Elenchi di termini-eliminazione |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/Read | Elenchi di termini-ottenere gli elenchi a tutti i termini-ottenere i dettagli |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/refreshindex/Action | Elenchi di termini-Aggiorna indice di ricerca |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/terms/Delete | Termine-Elimina-termine-Elimina tutti i termini |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/terms/Read | Termine-ottenere tutti i termini |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/termini/scrittura | Termine-termine aggiunta |
> | DataAction | Microsoft. CognitiveServices/accounts/ContentModerator/termlists/Write | Elenchi di termini-Dettagli aggiornamento |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/classifica/iterazioni/immagine/azione | Classificare un'immagine e salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/classifica/iterazioni/immagine/NoStore/azione | Classificare un'immagine senza salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/classifica/iterazioni/URL/azione | Classificare un URL di immagine e salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/classificate/iterazioni/URL/NoStore/azione | Classificare un URL di immagine senza salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Detect/iteraziones/image/Action | Rilevare gli oggetti in un'immagine e salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Detect/iteraziones/image/NoStore/Action | Rilevare gli oggetti in un'immagine senza salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Detect/iteraziones/URL/Action | Rilevare gli oggetti in un URL di immagine e salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Detect/iteraziones/URL/NoStore/Action | Rilevare gli oggetti in un URL di immagine senza salvare il risultato. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Domains/Read | Ottenere informazioni su un dominio specifico. Ottenere un elenco dei domini disponibili. * |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/labelproposals/setting/Action | Imposta le dimensioni del pool della proposta di etichetta. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/labelproposals/setting/Read | Ottiene le dimensioni del pool della proposta di etichetta per il progetto. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Project/migrate/Action | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Action | Creare un progetto. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Delete | Eliminare un progetto specifico. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Action | Questa API accetta il contenuto del corpo come multipart/form-data e Application/ottetto-Stream. Quando si usa multipart |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/progetti/images/Delete | Elimina le immagini dal set di immagini di training. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/files/Action | Questa API accetta un batch di file e, facoltativamente, tag per la creazione di immagini. È previsto un limite di 64 immagini e 20 tag. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/ID/Read | Questa API restituirà un set di immagini per i tag specificati e, facoltativamente, l'iterazione. Se non viene specificata alcuna iterazione |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Predictions/Action | Questa API crea un batch di immagini dalle immagini stimate specificate. È previsto un limite di 64 immagini e 20 tag. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/regionproposals/Action | Questa API otterrà proposte di area per un'immagine insieme a confidenze per l'area. Restituisce una matrice vuota se non viene trovata alcuna proposta. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects, images/Regions/Action | Questa API accetta un batch di aree immagine e, facoltativamente, i tag, per aggiornare le immagini esistenti con le informazioni sull'area. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects, images/Regions/Delete | Eliminare un set di aree dell'immagine. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/suggested/Action | Questa API recupererà le immagini senza tag filtrate dagli ID dei tag suggeriti. Restituisce una matrice vuota se non viene trovata alcuna immagine. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/suggested/count/Action | Questa API accetta tagIds per ottenere il numero di immagini senza tag per ogni tag suggerito per una determinata soglia. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Tagged/count/Read | Il filtro si trova in una relazione and/or. Ad esempio, se gli ID di tag specificati sono per "Dog" e |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Tagged/Read | Questa API supporta l'invio in batch e l'intervallo di selezione. Per impostazione predefinita, restituirà solo le prime immagini 50 corrispondenti alle immagini. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Tags/Action | Associare un set di immagini a un set di tag. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Tags/Delete | Rimuovere un set di tag da un set di immagini. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Untagged/count/Read | Questa API restituisce le immagini che non contengono tag per un determinato progetto ed eventualmente un'iterazione. Se non viene specificata alcuna iterazione |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/Untagged/Read | Questa API supporta l'invio in batch e l'intervallo di selezione. Per impostazione predefinita, restituirà solo le prime immagini 50 corrispondenti alle immagini. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/images/URL/Action | Questa API accetta un batch di URL e, facoltativamente, tag per la creazione di immagini. È previsto un limite di 64 immagini e 20 tag. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteration/Delete | Eliminare un'iterazione specifica di un progetto. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/Export/Action | Esportare un'iterazione sottoposta a training. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/Export/Read | Ottiene l'elenco delle esportazioni per un'iterazione specifica. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/performance/Images/count/Read | Il filtro si trova in una relazione and/or. Ad esempio, se gli ID di tag specificati sono per "Dog" e |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/performance/images/Read | Questa API supporta l'invio in batch e l'intervallo di selezione. Per impostazione predefinita, restituirà solo le prime immagini 50 corrispondenti alle immagini. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/Performance/Read | Ottenere informazioni dettagliate sulle prestazioni relative a un'iterazione. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/Publish/Action | Pubblicare un'iterazione specifica. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/Publish/Delete | Annulla la pubblicazione di un'iterazione specifica. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteraziones/Read | Ottenere un'iterazione specifica. Ottenere le iterazioni per il progetto. * |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/iteration/Write | Aggiornare un'iterazione specifica. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Predictions/Delete | Eliminare un set di immagini stimate e i risultati di stima associati. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Predictions/query/Action | Ottiene le immagini inviate all'endpoint di stima. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/QuickTest/image/Action | Test veloce di un'immagine. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/QuickTest/URL/Action | Testare rapidamente un URL di immagine. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Read | Ottenere un progetto specifico. Ottenere i progetti. * |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Tags/Action | Creare un tag per il progetto. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Tags/Delete | Eliminare un tag dal progetto. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Tags/Read | Ottenere informazioni su un tag specifico. Ottenere i tag per un progetto e un'iterazione specificati. * |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Tags/Write | Aggiornare un tag. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/tagsandregions/suggestions/Action | Questa API riceve i tag e le aree suggeriti per un array/batch di immagini senza tag insieme alle confidenze per i tag. Restituisce una matrice vuota se non viene trovato alcun tag. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Train/Action | Accoda il progetto per il training. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Projects/Write | Aggiornare un progetto specifico. |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/quota/Action | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/quota/Delete | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/quota/Refresh/Write | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Usage/Prediction/User/Read | Ottenere l'utilizzo per la risorsa di stima per l'utente Oxford |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Usage/Training/Resource/Tier/Read | Ottenere l'utilizzo per la risorsa di training per l'utente di Azure |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Usage/Training/User/Read | Ottenere l'utilizzo per la risorsa di training per l'utente Oxford |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/User/Action | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/User/Delete | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/User/State/Write | Aggiorna stato utente |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/User/Tier/Write | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/Users/Read | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/whitelist/Delete | Elimina un utente incluso nella whitelist con funzionalità specifiche |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/whitelist/Read | Ottiene un elenco di utenti inclusi nella whitelist con funzionalità specifiche |
> | DataAction | Microsoft. CognitiveServices/accounts/CustomVision. Prediction/whitelist/Write | Aggiorna o crea un utente nell'elenco elementi consentiti con funzionalità specifiche |
> | Azione | Microsoft.CognitiveServices/accounts/delete | Elimina gli account delle API |
> | DataAction | Microsoft. CognitiveServices/accounts/EntitySearch/Search/Action | Ottiene le entità e inserisce i risultati per una query specificata. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Rilevare i visi umani in un'immagine, restituire rettangoli visi e, facoltativamente, faceIds, punti di riferimento e attributi. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Elimina un elenco di visi specificato. Verranno eliminate anche le immagini del volto correlate nell'elenco dei visi. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Eliminare una faccia da un elenco di visi in base a faceListId e persisitedFaceId specificati. Anche l'immagine della faccia correlata verrà eliminata. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Aggiungere una faccia a un elenco di visi specificato, fino a 1.000 visi. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Recuperare faceListId, Name, userData e visi di un elenco di facet nell'elenco dei visi. List Face elenca ' faceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Creare un elenco di facet vuoto con faceListId specificato dall'utente, nome e un userData facoltativo. Sono consentiti fino a 64 elenchi di visi per aggiornare le informazioni di un elenco di visi, inclusi nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Dato faceId del volto della query, per cercare i visi simili da un array faceId, un elenco di visi o un elenco di visi di grandi dimensioni. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Dividere i visi candidati in gruppi in base alla somiglianza del volto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | identificazione da 1 a molti per trovare le corrispondenze più vicine della persona di query specifica da un gruppo di persone o da un gruppo di persone di grandi dimensioni. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Elimina un elenco di facet di grandi dimensioni specificato. Verranno eliminate anche le immagini del volto correlate nell'elenco dei volti grandi. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Eliminare una faccia da un elenco di facet di grandi dimensioni in base a largeFaceListId e persisitedFaceId specificati. Anche l'immagine della faccia correlata verrà eliminata. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Recuperare il volto permanente in un elenco di visi di grandi dimensioni di largeFaceListId e persistedFaceId. Elenca i visi ' persistedFaceId e userData in un elenco di facet esteso specificato. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Aggiungere una faccia a un elenco di visi grande specificato, fino a 1 milione visi. Aggiorna il campo userData di un viso specificato in un elenco di visi di grandi dimensioni in base al relativo persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Recuperare il largeFaceListId, il nome e l'userData di un elenco di facet di grandi dimensioni. Elenca le informazioni relative agli elenchi di facet di grandi dimensioni di largeFaceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Inviare un'attività di training per un elenco di visi di grandi dimensioni. Il training è un passaggio fondamentale che può essere utilizzato solo da un elenco di visi con training. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Per controllare lo stato di training di un elenco di visi di grandi dimensioni completato o ancora in corso. Il training di LargeFaceList è un'operazione asincrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Creare un elenco di facciate grande vuoto con largeFaceListId specificato dall'utente, nome e un userData facoltativo. Aggiornare le informazioni di un elenco di facet di grandi dimensioni, inclusi nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Elimina un gruppo di persone di grandi dimensioni esistente con personGroupId specificato. I dati salvati in un gruppo di persone di grandi dimensioni verranno eliminati. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crea una nuova persona in un gruppo di persone di grandi dimensioni specificato. Per aggiungere la faccia a questa persona, chiamare |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Elimina una persona esistente da un gruppo di persone di grandi dimensioni. Tutti i dati delle persone archiviate e le immagini facciali nella voce person verranno eliminati. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Eliminare una faccia da una persona in un gruppo di persone di grandi dimensioni. Verranno eliminati anche i dati volti e l'immagine correlati a questa voce del volto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Recuperare le informazioni sul viso della persona. Il facet person permanente viene specificato da largePersonGroupId, personId e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Aggiungere un'immagine viso a una persona in un gruppo di persone di grandi dimensioni per l'identificazione della faccia o la verifica. Per gestire l'immagine dell'aggiornamento del campo userData del viso persistente di un utente. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Recuperare il nome e l'userData di una persona e il faceIds persistente che rappresenta l'immagine della persona registrata. Elencare tutte le informazioni relative alle persone nel gruppo di persone di grandi dimensioni specificato, tra cui personId, Name, userData e persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Aggiornare nome o userData di una persona. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Recuperare le informazioni di un gruppo di persone di grandi dimensioni, inclusi il nome e l'userData. Questa API restituisce le informazioni sul gruppo di persone di grandi dimensioni elencare tutti i largePesonGroupId, il nome e l'userData di tutti i gruppi di persone esistenti. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Inviare un'attività di formazione per gruppi di persone di grandi dimensioni. Il training è un passaggio fondamentale che può essere utilizzato solo da un gruppo di persone di grandi dimensioni. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Per controllare lo stato di training del gruppo di persone di grandi dimensioni completato o ancora in corso. Il training di LargePersonGroup è un'operazione asincrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Creare un nuovo gruppo di persone di grandi dimensioni con largePersonGroupId specificato dall'utente, nome e userData facoltativo. Aggiornare il nome e l'userData di un gruppo di persone di grandi dimensioni esistente. Le proprietà rimangono invariate se non sono nel corpo della richiesta. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Elimina un gruppo di persone esistente con il personGroupId specificato. I dati salvati in un gruppo person verranno eliminati. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Crea una nuova persona in un gruppo di persone specificato. Per aggiungere la faccia a questa persona, chiamare |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Eliminare una persona esistente da un gruppo di persone. Tutti i dati delle persone archiviate e le immagini facciali nella voce person verranno eliminati. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Eliminare una faccia da una persona in un gruppo di persone. Verranno eliminati anche i dati volti e l'immagine correlati a questa voce del volto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Recuperare le informazioni sul viso della persona. Il facet person permanente viene specificato da personGroupId, personId e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Aggiungere un'immagine viso a una persona in un gruppo di persone per l'identificazione della faccia o la verifica. Per gestire l'immagine di più aggiornamenti, il campo userData del viso persistente di un utente. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Recuperare il nome e l'userData di una persona e il faceIds persistente che rappresenta l'immagine della persona registrata. Elenca tutte le informazioni relative alle persone nel gruppo person specificato, inclusi personId, Name, userData e persistedFaceIds di registered. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Aggiornare nome o userData di una persona. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Recuperare il nome del gruppo di persone e userData. Per ottenere informazioni sulla persona in questa gruppo, usare pesonGroupId, nome e userData dei gruppi di persone. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Inviare un'attività di training gruppo di persone. Il training è un passaggio fondamentale che può essere utilizzato solo da un gruppo di persone sottoposto a training. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Per controllare lo stato di training del gruppo di persone completato o ancora in corso. Il training di gruppo è un'operazione asincrona attivata |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Creare un nuovo gruppo di persone con il personGroupId specificato, il nome e la userData fornita dall'utente. Aggiornare il nome e l'userData del gruppo di persone esistente. Le proprietà rimangono invariate se non sono nel corpo della richiesta. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Verificare se due visi appartengono alla stessa persona o se una faccia appartiene a una persona. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/Models/Analyze/Action | Estrae coppie chiave-valore da un documento specificato. Il documento di input deve essere di uno dei tipi di contenuto supportati, ovvero "application/pdf", "image/jpeg" o "image/png". Viene restituita una risposta con esito positivo in formato JSON. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/Models/Delete | Elimina gli artefatti del modello. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/Models/Keys/Read | Recuperare le chiavi per il modello. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/Models/Read | Ottenere informazioni su un modello. Ottenere informazioni su tutti i modelli personalizzati sottoposti a training * |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/Custom/Train/Action | Creare ed eseguire il training di un modello personalizzato.<br>La richiesta di training deve includere un parametro di origine che sia un URI del contenitore BLOB di archiviazione di Azure accessibile esternamente (preferibilmente un URI di firma di accesso condiviso) o un percorso valido di una cartella di dati in un'unità montata localmente.<br>Quando vengono specificati i percorsi locali, devono seguire il formato del percorso di Linux/Unix ed essere un percorso assoluto con la configurazione di montaggio di input |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/precompilata/ricevuta/asyncbatchanalyze/azione | Estrarre il testo del campo e i valori semantici da un documento di ricezione specificato. Il documento di immagine di input deve essere uno dei tipi di contenuto supportati, JPEG, PNG, BMP, PDF o TIFF. Una risposta di esito positivo è un file JSON contenente un campo denominato ' Operation-location ', che contiene l'URL per l'operazione Get Receipt result per recuperare i risultati in modo asincrono. |
> | DataAction | Microsoft. CognitiveServices/accounts/FormRecognizer/precompilata/ricevuta/operazioni/azione | Eseguire una query sullo stato e recuperare il risultato di un'operazione di ricezione dell'analisi. L'URL di questa interfaccia può essere ottenuto dall'intestazione ' Operation-location ' nella risposta di ricezione dell'analisi. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/Details/Action | Restituisce informazioni dettagliate su un'immagine, ad esempio le pagine Web che includono l'immagine. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/Search/Action | Ottenere le immagini rilevanti per una query specificata. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImageSearch/trend/Action | Ottenere immagini di tendenza. |
> | DataAction | Microsoft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Crea una sessione di lettura immersiva |
> | DataAction | Microsoft. CognitiveServices/accounts/InkRecognizer/Recognize/azione | Dato un set di dati Stroke, analizza il contenuto e genera un elenco di entità riconosciute, incluso il testo riconosciuto. |
> | Azione | Microsoft.CognitiveServices/accounts/listKeys/action | Elenco delle chiavi |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Ottiene la stima dell'endpoint pubblicato per la query specificata. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/categorysearch/Action | Restituisce le notizie per una categoria specificata. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/Search/Action | Ottenere articoli di notizie rilevanti per una determinata query. |
> | DataAction | Microsoft. CognitiveServices/accounts/NewsSearch/trendingtopics/Action | Ottenere gli argomenti di tendenza identificati da Bing. Questi sono gli stessi argomenti illustrati nel banner nella parte inferiore del home page Bing. |
> | Azione | Microsoft.CognitiveServices/accounts/read | Legge gli account delle API. |
> | Azione | Microsoft.CognitiveServices/accounts/regenerateKey/action | Rigenerazione della chiave |
> | Azione | Microsoft.CognitiveServices/accounts/skus/read | Legge gli SKU disponibili per una risorsa esistente. |
> | DataAction | Microsoft. CognitiveServices/accounts/SpellCheck/SpellCheck/Action | Ottenere il risultato di una query di controllo ortografico tramite GET o POST. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | L'API restituisce un elenco di entità note e di entità denominate generali (\"person\", \"location\", \"Organization\" e così via) in un documento specificato. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | L'API restituisce un elenco di stringhe che indicano i a punti principali del testo di input. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | L'API restituisce la lingua rilevata e un punteggio numerico compreso tra 0 e 1. I punteggi vicini all'1 indicano una certezza al 100% della correttezza della lingua identificata. Sono supportate in totale 120 lingue. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | L'API restituisce un punteggio numerico compreso tra 0 e 1.<br>I valori prossimi a 1 indicano una valutazione positiva, mentre i valori prossimi a 0 indicano una valutazione negativa.<br>Il Punteggio 0,5 indica la mancanza di sentimenti, ad esempio<br>un'istruzione del controllo del controllo. |
> | Azione | Microsoft.CognitiveServices/accounts/usages/read | Ottiene l'uso della quota per una risorsa esistente. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/Details/Action | Ottenere informazioni approfondite su un video, ad esempio video correlati. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/Search/Action | Ottiene i video pertinenti per una query specifica. |
> | DataAction | Microsoft. CognitiveServices/accounts/VideoSearch/trend/Action | Ottenere i video attualmente di tendenza. |
> | DataAction | Microsoft. CognitiveServices/accounts/VisualSearch/Search/Action | Restituisce un elenco di tag relativi all'immagine fornita |
> | DataAction | Microsoft. CognitiveServices/accounts/WebSearch, ricerca/azione | Ottenere risultati Web, immagini, notizie, & video per una determinata query. |
> | Azione | Microsoft.CognitiveServices/accounts/write | Scrive gli account delle API. |
> | Azione | Microsoft.CognitiveServices/checkDomainAvailability/action | Legge gli SKU disponibili per una sottoscrizione. |
> | Azione | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Legge gli SKU disponibili per una sottoscrizione. |
> | Azione | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Legge gli SKU disponibili per una sottoscrizione. |
> | Azione | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Notifica da Microsoft. Network dell'eliminazione di VirtualNetworks o subnet. |
> | Azione | Microsoft. CognitiveServices/locations/operationresults/Read | Legge lo stato di un'operazione asincrona. |
> | Azione | Microsoft.CognitiveServices/Operations/read | Elenca tutte le operazioni disponibili |
> | Azione | Microsoft.CognitiveServices/register/action | Registra la sottoscrizione per Servizi cognitivi |
> | Azione | Microsoft.CognitiveServices/register/action | Registra la sottoscrizione per Servizi cognitivi |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Commerce/RateCard/read | Restituisce dati dell'offerta, metadati delle risorse o del contatore e tariffe per una determinata sottoscrizione. |
> | Azione | Microsoft. Commerce/Register/Action | Registra la sottoscrizione per Microsoft Commerce UsageAggregate |
> | Azione | Microsoft. Commerce/Annulla registrazione/azione | Annulla la registrazione della sottoscrizione per Microsoft Commerce UsageAggregate |
> | Azione | Microsoft.Commerce/UsageAggregates/read | Recupera il consumo di Microsoft Azure da una sottoscrizione. Il risultato contiene dati di utilizzo delle aggregazioni, informazioni correlate a sottoscrizioni e risorse in un intervallo di tempo specifico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Compute/availabilitySets/delete | Elimina il set di disponibilità |
> | Azione | Microsoft.Compute/availabilitySets/read | Ottiene le proprietà di un set di disponibilità |
> | Azione | Microsoft.Compute/availabilitySets/vmSizes/read | Elenca le dimensioni disponibili per creare o aggiornare una macchina virtuale nel set di disponibilità |
> | Azione | Microsoft.Compute/availabilitySets/write | Crea un nuovo set di disponibilità o ne aggiorna uno esistente |
> | Azione | Microsoft. Compute/diskEncryptionSets/Delete | Eliminare un set di crittografia dischi |
> | Azione | Microsoft. Compute/diskEncryptionSets/Read | Ottenere le proprietà di un set di crittografia del disco |
> | Azione | Microsoft. Compute/diskEncryptionSets/Write | Crea un nuovo set di crittografia del disco o ne aggiorna uno esistente |
> | Azione | Microsoft.Compute/disks/beginGetAccess/action | Ottiene gli URI SAS del disco per l’accesso al BLOB |
> | Azione | Microsoft.Compute/disks/delete | Elimina il disco |
> | Azione | Microsoft.Compute/disks/endGetAccess/action | Revoca l’URI SAS del disco |
> | Azione | Microsoft.Compute/disks/read | Ottiene le proprietà di un disco |
> | Azione | Microsoft.Compute/disks/write | Crea un nuovo disco o ne aggiorna uno esistente |
> | Azione | Microsoft. Compute/Galleries/Applications/Delete | Elimina l'applicazione della raccolta |
> | Azione | Microsoft. Compute/gallerys/Applications/Read | Ottiene le proprietà dell'applicazione della raccolta |
> | Azione | Microsoft. Compute/gallerys/Applications/Versions/Delete | Elimina la versione dell'applicazione della raccolta |
> | Azione | Microsoft. Compute/gallerys/Applications/Versions/Read | Ottiene le proprietà della versione dell'applicazione della raccolta |
> | Azione | Microsoft. Compute/gallerys/Applications/Versions/Write | Crea una nuova versione dell'applicazione della raccolta o ne aggiorna una esistente |
> | Azione | Microsoft. Compute/gallerys/Applications/Write | Crea una nuova applicazione della raccolta o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/galleries/delete | Elimina la raccolta |
> | Azione | Microsoft.Compute/galleries/images/delete | Elimina l'immagine della raccolta |
> | Azione | Microsoft.Compute/galleries/images/read | Ottiene le proprietà dell'immagine della raccolta |
> | Azione | Microsoft.Compute/galleries/images/versions/delete | Elimina la versione dell'immagine della raccolta |
> | Azione | Microsoft.Compute/galleries/images/versions/read | Ottiene le proprietà della versione dell'immagine della raccolta |
> | Azione | Microsoft.Compute/galleries/images/versions/write | Crea una nuova versione dell'immagine della raccolta o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/galleries/images/write | Crea una nuova immagine della raccolta o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/galleries/read | Ottiene le proprietà della raccolta |
> | Azione | Microsoft.Compute/galleries/write | Crea una nuova raccolta o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/hostGroups/delete | Elimina il gruppo host |
> | Azione | Microsoft. Compute/gruppi host/hosts/Delete | Elimina l'host |
> | Azione | Microsoft. Compute/gruppi host/hosts/Read | Ottenere le proprietà di un host |
> | Azione | Microsoft. Compute/gruppi host/hosts/Write | Crea un nuovo host o ne aggiorna uno esistente |
> | Azione | Microsoft.Compute/hostGroups/read | Ottenere le proprietà di un gruppo host |
> | Azione | Microsoft.Compute/hostGroups/write | Crea un nuovo gruppo host o aggiorna un gruppo host esistente |
> | Azione | Microsoft.Compute/images/delete | Elimina l'immagine |
> | Azione | Microsoft.Compute/images/read | Ottiene le proprietà dell’immagine |
> | Azione | Microsoft.Compute/images/write | Crea una nuova immagine o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/locations/capsOperations/read | Ottiene lo stato di un'operazione Caps asincrona |
> | Azione | Microsoft.Compute/locations/diskOperations/read | Ottiene lo stato di un'operazione Disk asincrona |
> | Azione | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Consente di creare i log per mostrare le richieste totali per intervallo di tempo per la diagnostica della limitazione. |
> | Azione | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Consente di creare i log per mostrare i dati aggregati delle richieste limitate raggruppate per nome risorsa, nome operazione o per i criteri di limitazione applicati. |
> | Azione | Microsoft.Compute/locations/operations/read | Ottiene lo stato di un'operazione asincrona |
> | Azione | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Ottiene le proprietà di un'offerta dell'immagine della piattaforma |
> | Azione | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Ottiene le proprietà di uno SKU dell'immagine della piattaforma |
> | Azione | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Ottiene le proprietà di una versione dell'immagine della piattaforma |
> | Azione | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Ottiene le proprietà di un tipo VMExtension |
> | Azione | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Ottiene le proprietà di una versione VMExtension |
> | Azione | Microsoft.Compute/locations/publishers/read | Ottiene le proprietà di un server di pubblicazione |
> | Azione | Microsoft.Compute/locations/runCommands/read | Elenca i comandi di esecuzione disponibili nella posizione |
> | Azione | Microsoft.Compute/locations/usages/read | Ottiene i limiti del servizio e le quantità di utilizzo corrente delle risorse di calcolo della sottoscrizione in una posizione |
> | Azione | Microsoft.Compute/locations/vmSizes/read | Elenca le dimensioni delle macchine virtuali disponibili in una posizione |
> | Azione | Microsoft. Compute/locations/vsmOperations/Read | Ottiene lo stato di un'operazione asincrona per il set di scalabilità di macchine virtuali con l'estensione del servizio di runtime della macchina virtuale |
> | Azione | Microsoft.Compute/operations/read | Elenca le operazioni disponibili sul provider di risorse Microsoft.Compute |
> | Azione | Microsoft.Compute/proximityPlacementGroups/delete | Elimina il gruppo di posizionamento vicino |
> | Azione | Microsoft.Compute/proximityPlacementGroups/read | Ottenere le proprietà di un gruppo di posizionamento di prossimità |
> | Azione | Microsoft.Compute/proximityPlacementGroups/write | Crea un nuovo gruppo di posizionamento di prossimità o ne aggiorna uno esistente |
> | Azione | Microsoft.Compute/register/action | Registra la sottoscrizione con il provider di risorse Microsoft.Compute |
> | Azione | Microsoft.Compute/restorePointCollections/delete | Elimina la raccolta di punti di ripristino e i punti di ripristino contenuti |
> | Azione | Microsoft.Compute/restorePointCollections/read | Ottiene le proprietà di un insieme di punti di ripristino |
> | Azione | Microsoft.Compute/restorePointCollections/restorePoints/delete | Elimina il punto di ripristino |
> | Azione | Microsoft.Compute/restorePointCollections/restorePoints/read | Ottiene le proprietà di un punto di ripristino |
> | Azione | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Ottiene le proprietà di un punto di ripristino insieme agli URI SAS del BLOB |
> | Azione | Microsoft.Compute/restorePointCollections/restorePoints/write | Crea un nuovo punto di ripristino |
> | Azione | Microsoft.Compute/restorePointCollections/write | Crea un nuovo insieme di punti di ripristino o aggiorna un insieme esistente |
> | Azione | Microsoft.Compute/sharedVMImages/delete | Elimina la SharedVMImage |
> | Azione | Microsoft.Compute/sharedVMImages/read | Ottiene le proprietà di una SharedVMImage |
> | Azione | Microsoft.Compute/sharedVMImages/versions/delete | Elimina una versione di immagine della macchina virtuale condivisa |
> | Azione | Microsoft.Compute/sharedVMImages/versions/read | Ottiene le proprietà di una versione di immagine della macchina virtuale condivisa |
> | Azione | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replica una versione di immagine della macchina virtuale condivisa per le aree di destinazione |
> | Azione | Microsoft.Compute/sharedVMImages/versions/write | Crea una nuova versione di immagine della macchina virtuale condivisa o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/sharedVMImages/write | Crea una nuova SharedVMImage o aggiornarne una esistente |
> | Azione | Microsoft.Compute/skus/read | Ottiene l'elenco degli SKU Microsoft.Compute disponibili per la sottoscrizione |
> | Azione | Microsoft.Compute/snapshots/beginGetAccess/action | Ottiene l'URI SAS dello snapshot per l'accesso al BLOB |
> | Azione | Microsoft.Compute/snapshots/delete | Elimina una snapshot |
> | Azione | Microsoft.Compute/snapshots/endGetAccess/action | Revoca l'URI SAS dello snapshot |
> | Azione | Microsoft.Compute/snapshots/read | Ottiene le proprietà di una snapshot |
> | Azione | Microsoft.Compute/snapshots/write | Crea una nuova snapshot o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/unregister/action | Annulla la registrazione della sottoscrizione con il provider di risorse Microsoft. Compute |
> | Azione | Microsoft.Compute/virtualMachines/capture/action | Acquisisce la macchina virtuale copiando i dischi rigidi virtuali e genera un modello utilizzabile per creare macchine virtuali simili |
> | Azione | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converte i dischi basati su BLOB della macchina virtuale in dischi gestiti |
> | Azione | Microsoft.Compute/virtualMachines/deallocate/action | Disabilita la macchina virtuale e rilascia le risorse di calcolo |
> | Azione | Microsoft.Compute/virtualMachines/delete | Elimina la macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/extensions/delete | Elimina l'estensione macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/extensions/read | Ottiene le proprietà di un'estensione macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/extensions/write | Crea una nuova estensione macchina virtuale o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/virtualMachines/generalize/action | Imposta lo stato della macchina virtuale a Generalizzato e prepara la macchina virtuale per l'acquisizione |
> | Azione | Microsoft.Compute/virtualMachines/instanceView/read | Ottiene lo stato di runtime dettagliato della macchina virtuale e delle relative risorse |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Consente di accedere a una macchina virtuale come utente normale |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Consente di accedere a una macchina virtuale con privilegi di amministratore di Windows o di utente root Linux |
> | Azione | Microsoft.Compute/virtualMachines/performMaintenance/action | Esegue operazioni di manutenzione sulla VM. |
> | Azione | Microsoft.Compute/virtualMachines/powerOff/action | Disabilita la macchina virtuale. Notare che la macchina virtuale continuerà a essere fatturata. |
> | Azione | Microsoft.Compute/virtualMachines/read | Ottiene le proprietà di una macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/redeploy/action | Ridistribuisce la macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/reimage/action | Ricrea l'immagine della macchina virtuale che usa un disco differenze. |
> | Azione | Microsoft.Compute/virtualMachines/restart/action | Riavvia la macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/runCommand/action | Esegue uno script predefinito nella macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/start/action | Avvia la macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/vmSizes/read | Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale |
> | Azione | Microsoft.Compute/virtualMachines/write | Crea una nuova macchina virtuale o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Disabilita e rilascia le risorse di calcolo per le istanze del set di scalabilità di macchine virtuali  |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/delete | Elimina il set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/delete/action | Elimina le istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Elimina l'estensione set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Ottiene le proprietà di un'estensione set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Crea una nuova estensione set di scalabilità di macchine virtuali o ne aggiorna una esistente |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Esamina manualmente i domini di aggiornamento della piattaforma di un set di scalabilità di macchine virtuali dell'infrastruttura di servizi |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Ottiene la vista delle istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Aggiorna manualmente le istanze all'ultimo modello del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Ottiene le proprietà di tutte le interfacce di rete di un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Avvia un aggiornamento in sequenza per spostare tutte le istanze del set di scalabilità di macchine virtuali alla versione disponibile più recente del sistema operativo per l'immagine della piattaforma. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Recupera la cronologia degli aggiornamenti del sistema operativo per un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Esegue la manutenzione pianificata sulle istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Disabilita le istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Ottiene le proprietà di tutti gli indirizzi IP pubblici di un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/read | Ottiene le proprietà di un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Ridistribuisce le istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Ricrea l'immagine delle istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Ricrea l'immagine di tutti i dischi (disco del sistema operativo e dischi dati) per le istanze di un set di scalabilità di macchine virtuali  |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/restart/action | Riavvia le istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Annulla l'aggiornamento in sequenza di un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Recupera lo stato più recente dell'aggiornamento in sequenza per un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verifica se un set di scalabilità di macchine virtuali esistente può ridurre/aumentare il numero di istanze fino al numero di istanze specificato |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/skus/read | Elenca gli SKU validi per un set di scalabilità di macchine virtuali esistente |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/start/action | Avvia le istanze del set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Disabilita e rilascia le risorse di calcolo per una macchina virtuale in un set di scalabilità VM. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Elimina una specifica macchina virtuale in un set di scalabilità VM. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Recupera la vista istanza di una macchina virtuale in un set di scalabilità di una VM. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Ottiene le proprietà degli indirizzi IP pubblici creati usando un set di scalabilità di macchine virtuali. Il set di scalabilità di macchine virtuali può creare al massimo un indirizzo IP pubblico per ogni configurazione IP, ovvero IP privati |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Ottiene le proprietà di una o tutte le configurazioni IP di un'interfaccia di rete creata usando un ser di scalabilità di macchine virtuali. Le configurazioni IP rappresentano indirizzi IP privati |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Ottiene le proprietà di una o tutte le interfacce di rete di una macchina virtuale creata usando un ser di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Esegue la manutenzione pianificata su un'istanza di macchina virtuale in un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Disabilita un'istanza di macchina virtuale in un set di scalabilità VM. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Recupera le proprietà di una macchina virtuale in un set di scalabilità VM |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Ridistribuisce l'immagine dell'istanza di una macchina virtuale in un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Ricrea l'immagine dell'istanza di una macchina virtuale in un set di scalabilità di macchine virtuali. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Ricrea l'immagine di tutti i dischi (disco del sistema operativo e dischi dati) per l'istanza di macchina virtuale in un set di scalabilità di macchine virtuali. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Riavvia un'istanza di macchina virtuale in un set di scalabilità VM. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Esegue uno script predefinito in un'istanza di macchina virtuale in un set di scalabilità di macchine virtuali. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Avvia un'istanza di macchina virtuale in un set di scalabilità VM. |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Aggiorna le proprietà di una macchina virtuale in un set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Elencare le dimensioni disponibili per la creazione o l'aggiornamento di una macchina virtuale nel set di scalabilità di macchine virtuali |
> | Azione | Microsoft.Compute/virtualMachineScaleSets/write | Crea un nuovo set di scalabilità di macchine virtuali o ne aggiorna uno esistente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. consumer/aggregatedcost/Read | Elencare AggregatedCost per il gruppo di gestione. |
> | Azione | Microsoft.Consumption/balances/read | Elenca il riepilogo di utilizzo per un periodo di fatturazione per un gruppo di gestione. |
> | Azione | Microsoft.Consumption/budgets/delete | Elimina i budget per una sottoscrizione o un gruppo di gestione. |
> | Azione | Microsoft.Consumption/budgets/read | Elenca i budget per una sottoscrizione o un gruppo di gestione. |
> | Azione | Microsoft.Consumption/budgets/write | Crea e aggiorna i budget per una sottoscrizione o un gruppo di gestione. |
> | Azione | Microsoft.Consumption/charges/read | Elenca gli addebiti |
> | Azione | Microsoft.Consumption/credits/read | Elenca i crediti |
> | Azione | Microsoft.Consumption/events/read | Elenca gli eventi |
> | Azione | Microsoft. consumer/externalBillingAccounts/Tags/Read | Elenca i tag per EA e sottoscrizioni. |
> | Azione | Microsoft. consumer/externalSubscriptions/Tags/Read | Elenca i tag per EA e sottoscrizioni. |
> | Azione | Microsoft.Consumption/forecasts/read | Elenca le previsioni |
> | Azione | Microsoft.Consumption/lots/read | Elenca i lotti |
> | Azione | Microsoft.Consumption/marketplaces/read | Elenca i dettagli di utilizzo della risorsa del marketplace per un ambito per sottoscrizioni EA e WebDirect. |
> | Azione | Microsoft.Consumption/operationresults/read | Elenca i risultati delle operazioni |
> | Azione | Microsoft.Consumption/operations/read | Elenca tutte le operazioni supportate per provider di risorse Microsoft.Consumption. |
> | Azione | Microsoft.Consumption/operationstatus/read | Elenca lo stato delle operazioni |
> | Azione | Microsoft.Consumption/pricesheets/read | Elenca i dati degli elenchi prezzi per una sottoscrizione o un gruppo di gestione. |
> | Azione | Microsoft.Consumption/register/action | Esegue la registrazione nel provider di risorse Consumption |
> | Azione | Microsoft.Consumption/reservationDetails/read | Elenca i dettagli di utilizzo per le istanze riservate per ordine di prenotazione o gruppi di gestione. I dati dei dettagli sono a livello di istanza e giorno. |
> | Azione | Microsoft.Consumption/reservationRecommendations/read | Elenca le raccomandazioni singole o condivise relative alle istanze riservate per una sottoscrizione. |
> | Azione | Microsoft.Consumption/reservationSummaries/read | Elenca il riepilogo di utilizzo per le istanze riservate per ordine di prenotazione o gruppi di gestione. I dati di riepilogo sono a livello mensile o giornaliero. |
> | Azione | Microsoft.Consumption/reservationTransactions/read | Elenca la cronologia delle transazioni per le istanze riservate per gruppi di gestione. |
> | Azione | Microsoft.Consumption/tags/read | Elenca i tag per EA e sottoscrizioni. |
> | Azione | Microsoft.Consumption/tenants/read | Elencare i tenant |
> | Azione | Microsoft.Consumption/tenants/register/action | Registra l'azione per l'ambito di Microsoft.Consumption da parte di un tenant. |
> | Azione | Microsoft.Consumption/terms/read | Elenca i termini per una sottoscrizione o un gruppo di gestione. |
> | Azione | Microsoft.Consumption/usageDetails/read | Elenca i dettagli di utilizzo per un ambito per sottoscrizioni EA e WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. ContainerInstance/containerGroups/Containers/buildlogs/Read | Ottenere i log di compilazione per un contenitore specifico. |
> | Azione | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Esegue in un contenitore specifico. |
> | Azione | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Recupera i log per un contenitore specifico. |
> | Azione | Microsoft.ContainerInstance/containerGroups/delete | Elimina il gruppo di contenitori specifico. |
> | Azione | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Ottenere il risultato dell'operazione asincrona |
> | Azione | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per il gruppo di contenitori. |
> | Azione | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Crea e aggiorna l'impostazione di diagnostica per il gruppo di contenitori. |
> | Azione | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per il gruppo di contenitori. |
> | Azione | Microsoft.ContainerInstance/containerGroups/read | Ottiene tutti i gruppi di contenitori. |
> | Azione | Microsoft.ContainerInstance/containerGroups/restart/action | Riavvia un gruppo di contenitori specifico. |
> | Azione | Microsoft.ContainerInstance/containerGroups/start/action | Avvia un gruppo di contenitori specifico. |
> | Azione | Microsoft.ContainerInstance/containerGroups/stop/action | Arresta un gruppo di contenitori specifico. Le risorse di calcolo verranno deallocate e la fatturazione verrà interrotta. |
> | Azione | Microsoft.ContainerInstance/containerGroups/write | Crea o aggiorna un gruppo di contenitori specifico. |
> | Azione | Microsoft.ContainerInstance/locations/cachedImages/read | Ottiene le immagini memorizzate nella cache per la sottoscrizione in un'area. |
> | Azione | Microsoft.ContainerInstance/locations/capabilities/read | Ottiene le funzionalità per un'area. |
> | Azione | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifica a Microsoft.ContainerInstance che è in corso l'eliminazione della rete virtuale o delle subnet. |
> | Azione | Microsoft. ContainerInstance/percorsi/operazioni/lettura | Elencare le operazioni per il servizio istanza di contenitore di Azure. |
> | Azione | Microsoft.ContainerInstance/locations/usages/read | Ottiene l'utilizzo per un'area specifica. |
> | Azione | Microsoft. ContainerInstance/Operations/Read | Elencare le operazioni per il servizio istanza di contenitore di Azure. |
> | Azione | Microsoft.ContainerInstance/register/action | Registra la sottoscrizione per il provider di risorse istanza di contenitore e abilita la creazione di gruppi di contenitori. |
> | Azione | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Eliminare il collegamento dell'associazione di servizi creato dal provider di risorse dell'istanza di contenitore di Azure in una subnet. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ContainerRegistry/checkNameAvailability/read | Controlla se il nome del registro contenitori è disponibile per l'uso. |
> | Azione | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Avvisa Microsoft.ContainerRegistry che la rete virtuale o la subnet è in fase di eliminazione |
> | Azione | Microsoft.ContainerRegistry/locations/operationResults/read | Ottiene il risultato di un'operazione asincrona |
> | Azione | Microsoft.ContainerRegistry/operations/read | Elenca tutte le operazioni API REST di Registro Azure Container disponibili |
> | Azione | Microsoft.ContainerRegistry/register/action | Registra la sottoscrizione per il provider di risorse registro contenitori e consente la creazione di registri contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/artifacts/delete | Elimina l'artefatto in un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/builds/cancel/action | Annulla una compilazione esistente. |
> | Azione | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Ottiene un collegamento per scaricare i log di compilazione. |
> | Azione | Microsoft.ContainerRegistry/registries/builds/read | Ottiene le proprietà della build specificata o elenca tutte le build per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/builds/write | Aggiorna una build per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/delete | Elimina un'attività di compilazione da un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Elenca le proprietà controllo del codice sorgente per un'attività di compilazione. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/read | Ottiene le proprietà dell'attività di compilazione specificata o elenca tutte le attività di compilazione per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Elimina un'istruzione di generazione da un'attività di compilazione. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Elenca gli argomenti di compilazione per una istruzione di generazione, inclusi gli argomenti del segreto. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Ottiene le proprietà dell'istruzione di generazione specificata o elenca tutte le istruzioni di generazione per l'attività di compilazione specificata. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Crea o aggiorna un'istruzione di generazione per un'attività di compilazione con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/buildTasks/write | Crea o aggiorna un'attività di compilazione per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/delete | Elimina un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Elimina un filtro di Griglia di eventi da un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Ottiene le proprietà del filtro di Griglia di eventi specificato o elenca tutti i filtri di Griglia di eventi per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Crea o aggiorna un filtro di Griglia di eventi per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft. ContainerRegistry/registris/generateCredentials/Action | Genera chiavi per un token di un registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Ottiene il percorso di caricamento perché l'utente possa caricare l'origine. |
> | Azione | Microsoft.ContainerRegistry/registries/importImage/action | Importa un'immagine nel registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Ottiene la posizione dell'url di caricamento dell’origine per un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/listCredentials/action | Elenca le credenziali di accesso per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/listPolicies/read | Elenca i criteri per il registro contenitori specificato |
> | Azione | Microsoft.ContainerRegistry/registries/listUsages/read | Elenca gli utilizzi di quota per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/metadata/read | Ottiene i metadati di un repository specifico per un registro contenitori |
> | Azione | Microsoft.ContainerRegistry/registries/metadata/write | Aggiorna i metadati di un repository per un registro contenitori |
> | Azione | Microsoft.ContainerRegistry/registries/operationStatuses/read | Ottiene uno stato operazione registry async |
> | Azione | Microsoft.ContainerRegistry/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/push/write | Eseguire il push o scrivere immagini in un registro contenitori. |
> | Azione | Microsoft. ContainerRegistry/registri/quarantena/lettura | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | Azione | Microsoft. ContainerRegistry/registri/quarantena/scrittura | Scrivere/modificare lo stato di quarantena di immagini in quarantena |
> | Azione | Microsoft.ContainerRegistry/registries/queueBuild/action | Crea una nuova build in base ai parametri di richiesta e l'aggiunge alla coda di compilazione. |
> | Azione | Microsoft.ContainerRegistry/registries/read | Ottiene le proprietà del registro contenitori specificato o elenca tutti i registri contenitori sotto il gruppo di risorse o la sottoscrizione specificata. |
> | Azione | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Rigenera una delle credenziali di accesso per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/replications/delete | Elimina una replica da un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Ottiene lo stato di un'operazione asincrona di replica |
> | Azione | Microsoft.ContainerRegistry/registries/replications/read | Ottiene le proprietà della replica specificato o elenca tutte le repliche per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/replications/write | Crea o aggiorna una replica per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/runs/cancel/action | Annulla un'esecuzione esistente. |
> | Azione | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Restituisce l'URL SAS di log per un'esecuzione. |
> | Azione | Microsoft.ContainerRegistry/registries/runs/read | Restituisce le proprietà di un'esecuzione rispetto a un registro contenitori o un elenco esecuzioni. |
> | Azione | Microsoft.ContainerRegistry/registries/runs/write | Aggiorna un’esecuzione. |
> | Azione | Microsoft.ContainerRegistry/registries/scheduleRun/action | Pianificare un'esecuzione in un registro contenitori. |
> | Azione | Microsoft. ContainerRegistry/registris/scopeMaps/Delete | Elimina una mappa dell'ambito da un registro contenitori. |
> | Azione | Microsoft. ContainerRegistry/registris/scopeMaps/operationStatuses/Read | Ottiene lo stato di un'operazione asincrona della mappa dell'ambito. |
> | Azione | Microsoft. ContainerRegistry/registris/scopeMaps/Read | Ottiene le proprietà della mappa ambito specificata o elenca tutte le mappe dell'ambito per il registro contenitori specificato. |
> | Azione | Microsoft. ContainerRegistry/registris/scopeMaps/Write | Crea o aggiorna una mappa dell'ambito per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/sign/write | Eseguire il push/pull di metadati considerati attendibili per un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/tasks/delete | Elimina un’attività per un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Elenca tutti i dettagli di un’attività per un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/tasks/read | Restituisce un'attività per un registro contenitori o elenca tutte le attività. |
> | Azione | Microsoft.ContainerRegistry/registries/tasks/write | Crea o aggiorna un’attività per un registro contenitori. |
> | Azione | Microsoft. ContainerRegistry/registri/token/Delete | Elimina un token da un registro contenitori. |
> | Azione | Microsoft. ContainerRegistry/registri/token/operationStatuses/lettura | Ottiene lo stato di un'operazione asincrona del token. |
> | Azione | Microsoft. ContainerRegistry/registri/token/lettura | Ottiene le proprietà del token specificato o elenca tutti i token per il registro contenitori specificato. |
> | Azione | Microsoft. ContainerRegistry/registri/token/scrittura | Crea o aggiorna un token per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/updatePolicies/write | Aggiorna i criteri per il registro contenitori specificato |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/delete | Elimina un webhook da un registro contenitori. |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Ottiene la configurazione dell'URI del servizio e le intestazioni personalizzate per il webhook. |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Elenca gli eventi di recenti per il webhook specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Ottiene lo stato di un'operazione asincrona di webhook |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Attiva un evento ping da inviare al webhook. |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/read | Ottiene le proprietà del webhook specificato o elenca tutti i webhook per il registro contenitori specificato. |
> | Azione | Microsoft.ContainerRegistry/registries/webhooks/write | Crea o aggiorna un webhook per un registro contenitori con i parametri specificati. |
> | Azione | Microsoft.ContainerRegistry/registries/write | Crea o aggiorna un registro contenitori con i parametri specificati. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ContainerService/containerServices/delete | Elimina un servizio contenitore |
> | Azione | Microsoft.ContainerService/containerServices/read | Recupera un servizio contenitore |
> | Azione | Microsoft.ContainerService/containerServices/write | Crea un nuovo servizio contenitore o ne aggiorna uno esistente |
> | Azione | Microsoft.ContainerService/locations/operationresults/read | Ottiene lo stato del risultato di un'operazione asincrona |
> | Azione | Microsoft.ContainerService/locations/operations/read | Ottiene lo stato di un'operazione asincrona |
> | Azione | Microsoft.ContainerService/locations/orchestrators/read | Elenca gli agenti di orchestrazione supportati |
> | Azione | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Recupera il profilo di accesso per un cluster gestito in base al nome del ruolo con un'operazione di elenco delle credenziali |
> | Azione | Microsoft.ContainerService/managedClusters/accessProfiles/read | Recupera il profilo di accesso per un cluster gestito in base al nome del ruolo |
> | Azione | Microsoft.ContainerService/managedClusters/agentPools/delete | Elimina un pool di agenti |
> | Azione | Microsoft.ContainerService/managedClusters/agentPools/read | Ottiene un pool di agenti |
> | Azione | Microsoft. servizio contenitore/managedClusters/agentPools/upgradeProfiles/Read | Ottiene il profilo di aggiornamento del pool di agenti |
> | Azione | Microsoft.ContainerService/managedClusters/agentPools/write | Crea un nuovo pool di agenti o ne aggiorna uno esistente |
> | Azione | Microsoft. servizio contenitore/managedClusters/availableAgentPoolVersions/Read | Ottiene le versioni del pool di agenti disponibili del cluster |
> | Azione | Microsoft.ContainerService/managedClusters/delete | Elimina un cluster gestito |
> | Azione | Microsoft. servizio contenitore/managedClusters/Detectors/Read | Ottenere il rilevamento del cluster gestito |
> | Azione | Microsoft. servizio contenitore/managedClusters/diagnosticsState/Read | Ottiene lo stato di diagnostica del cluster |
> | Azione | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Elenca la credenziale clusterAdmin di un cluster gestito |
> | Azione | Microsoft. servizio contenitore/managedClusters/listClusterMonitoringUserCredential/Action | Elencare le credenziali di clusterMonitoringUser di un cluster gestito |
> | Azione | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Elenca la credenziale clusterUser di un cluster gestito |
> | Azione | Microsoft. servizio contenitore/managedClusters/privateEndpointConnectionsApproval/Action | Determina se l'utente può approvare una connessione all'endpoint privato |
> | Azione | Microsoft.ContainerService/managedClusters/read | Recupera un cluster gestito |
> | Azione | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Reimposta il profilo di AAD di un cluster gestito |
> | Azione | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Reimposta il profilo dell'entità servizio di un cluster gestito |
> | Azione | Microsoft. servizio contenitore/managedClusters/rotateClusterCertificates/Action | Ruotare i certificati di un cluster gestito |
> | Azione | Microsoft. servizio contenitore/managedClusters/upgradeProfiles/Read | Ottiene il profilo di aggiornamento del cluster |
> | Azione | Microsoft.ContainerService/managedClusters/write | Crea un nuovo cluster gestito o ne aggiorna uno esistente |
> | Azione | Microsoft.ContainerService/openShiftClusters/delete | Eliminare un cluster Open Shift |
> | Azione | Microsoft.ContainerService/openShiftClusters/read | Ottenere un cluster Open Shift |
> | Azione | Microsoft.ContainerService/openShiftClusters/write | Crea un nuovo cluster Open Shift o ne aggiorna uno esistente |
> | Azione | Microsoft.ContainerService/openShiftManagedClusters/delete | Eliminare un cluster gestito con spostamento aperto |
> | Azione | Microsoft.ContainerService/openShiftManagedClusters/read | Ottenere un cluster gestito con spostamento aperto |
> | Azione | Microsoft.ContainerService/openShiftManagedClusters/write | Crea un nuovo cluster gestito di Open Shift o ne aggiorna uno esistente |
> | Azione | Microsoft.ContainerService/operations/read | Elenca le operazioni disponibili sul provider di risorse Microsoft.ContainerService |
> | Azione | Microsoft.ContainerService/register/action | Registra la sottoscrizione con il provider di risorse Microsoft.ContainerService |
> | Azione | Microsoft.ContainerService/unregister/action | Annulla la registrazione della sottoscrizione con il provider di risorse Microsoft.ContainerService |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. CostManagement/Alerts/Write | Aggiornare gli avvisi. |
> | Azione | Microsoft.CostManagement/cloudConnectors/delete | Elimina il cloudConnector specificato. |
> | Azione | Microsoft.CostManagement/cloudConnectors/read | Elencare il cloudConnectors per l'utente autenticato. |
> | Azione | Microsoft.CostManagement/cloudConnectors/write | Crea o aggiorna il cloudConnector specificato. |
> | Azione | Microsoft.CostManagement/dimensions/read | Elenca tutte le dimensioni supportate in base a un ambito. |
> | Azione | Microsoft.CostManagement/exports/action | Esegue l'esportazione specificata. |
> | Azione | Microsoft.CostManagement/exports/delete | Elimina l'esportazione specificata. |
> | Azione | Microsoft.CostManagement/exports/read | Elenca le esportazioni in base all'ambito. |
> | Azione | Microsoft. CostManagement/exports/Run/Action | Eseguire le esportazioni. |
> | Azione | Microsoft.CostManagement/exports/write | Crea o aggiorna l'esportazione specificata. |
> | Azione | Microsoft. CostManagement/externalBillingAccounts/Dimension/Read | Elencare tutte le dimensioni supportate per BillingAccounts esterni. |
> | Azione | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Elencare il externalSubscriptions all'interno di un externalBillingAccount per l'utente autenticato. |
> | Azione | Microsoft. CostManagement/externalBillingAccounts/previsione/azione | Prevedere i dati di utilizzo per BillingAccounts esterni. |
> | Azione | Microsoft. CostManagement/externalBillingAccounts/Forecast/Read | Prevedere i dati di utilizzo per BillingAccounts esterni. |
> | Azione | Microsoft. CostManagement/externalBillingAccounts/query/azione | Eseguire query sui dati di utilizzo per BillingAccounts esterni. |
> | Azione | Microsoft. CostManagement/externalBillingAccounts/query/read | Eseguire query sui dati di utilizzo per BillingAccounts esterni. |
> | Azione | Microsoft.CostManagement/externalBillingAccounts/read | Elencare il externalBillingAccounts per l'utente autenticato. |
> | Azione | Microsoft. CostManagement/externalSubscriptions/Dimensions/Read | Elencare tutte le dimensioni supportate per la sottoscrizione esterna. |
> | Azione | Microsoft. CostManagement/externalSubscriptions/previsione/azione | Prevedere i dati di utilizzo per BillingAccounts esterni. |
> | Azione | Microsoft. CostManagement/externalSubscriptions/Forecast/Read | Prevedere i dati di utilizzo per BillingAccounts esterni. |
> | Azione | Microsoft. CostManagement/externalSubscriptions/query/azione | Eseguire query sui dati di utilizzo per una sottoscrizione esterna. |
> | Azione | Microsoft. CostManagement/externalSubscriptions/query/read | Eseguire query sui dati di utilizzo per una sottoscrizione esterna. |
> | Azione | Microsoft.CostManagement/externalSubscriptions/read | Elencare il externalSubscriptions per l'utente autenticato. |
> | Azione | Microsoft. CostManagement/externalSubscriptions/Write | Aggiornare il gruppo di gestione di externalSubscription associato |
> | Azione | Microsoft. CostManagement/previsione/azione | Prevedere i dati di utilizzo in base a un ambito. |
> | Azione | Microsoft. CostManagement/Forecast/Read | Prevedere i dati di utilizzo in base a un ambito. |
> | Azione | Microsoft. CostManagement/Operations/Read | Elenca tutte le operazioni supportate dal provider di risorse Microsoft. CostManagement. |
> | Azione | Microsoft.CostManagement/query/action | Esegue una query sui dati di utilizzo in base a un ambito. |
> | Azione | Microsoft.CostManagement/query/read | Esegue una query sui dati di utilizzo in base a un ambito. |
> | Azione | Microsoft.CostManagement/register/action | Registrare l'azione per l'ambito di Microsoft. CostManagement da una sottoscrizione. |
> | Azione | Microsoft.CostManagement/reports/action | Pianifica i report sui dati di utilizzo in base a un ambito. |
> | Azione | Microsoft.CostManagement/reports/read | Pianifica i report sui dati di utilizzo in base a un ambito. |
> | Azione | Microsoft.CostManagement/tenants/register/action | Registrare l'azione per l'ambito di Microsoft. CostManagement da un tenant. |
> | Azione | Microsoft. CostManagement/views/Action | Crea vista. |
> | Azione | Microsoft. CostManagement/views/Delete | Elimina le visualizzazioni salvate. |
> | Azione | Microsoft. CostManagement/views/Read | Elencare tutte le visualizzazioni salvate. |
> | Azione | Microsoft. CostManagement/views/Write | Aggiornare la vista. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Consente di prenotare un ritiro per le spedizioni di ritorno. |
> | Azione | Microsoft.DataBox/jobs/cancel/action | Annulla un ordine in corso. |
> | Azione | Microsoft.DataBox/jobs/delete | Elimina gli ordini |
> | Azione | Microsoft.DataBox/jobs/listCredentials/action | Elenca le credenziali non crittografate correlate all'ordine. |
> | Azione | Microsoft.DataBox/jobs/read | Elenca o ottiene gli ordini |
> | Azione | Microsoft.DataBox/jobs/write | Crea o aggiorna gli ordini |
> | Azione | Microsoft.DataBox/locations/availableSkus/action | Restituisce l'elenco degli SKU disponibili. |
> | Azione | Microsoft. DataBox/locations/availableSkus/Read | Elencare o ottenere gli SKU disponibili |
> | Azione | Microsoft.DataBox/locations/operationResults/read | Elenca o ottiene i risultati delle operazioni |
> | Azione | Microsoft. DataBox/locations/regionConfiguration/Action | Questo metodo restituisce le configurazioni per l'area. |
> | Azione | Microsoft.DataBox/locations/validateAddress/action | Convalida l'indirizzo di spedizione e fornisce indirizzi alternativi, se disponibili. |
> | Azione | Microsoft. DataBox/locations/validateInputs/Action | Questo metodo esegue tutti i tipi di convalida. |
> | Azione | Microsoft. DataBox/Operations/Read | Elencare o ottenere le operazioni |
> | Azione | Microsoft.DataBox/register/action | Registra il provider Microsoft.Databox |
> | Azione | Microsoft. DataBox/subscriptions/resourceGroups/moveResources/Action | Questo metodo esegue lo spostamento delle risorse. |
> | Azione | Microsoft. DataBox/subscriptions/resourceGroups/validateMoveResources/Action | Questo metodo consente di verificare se lo spostamento di risorse è consentito o meno. |
> | Azione | Microsoft. DataBox/Annulla registrazione/azione | Annulla la registrazione del provider Microsoft. databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Elenca o ottiene gli avvisi |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Elimina le pianificazioni della larghezza di banda |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Elenca o ottiene le pianificazioni della larghezza di banda |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Elenca o ottiene le pianificazioni della larghezza di banda |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Crea o aggiorna le pianificazioni della larghezza di banda |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Elimina i dispositivi Data Box Edge |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Scarica gli aggiornamenti nel dispositivo |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Recupera le Informazioni estese sulle risorse |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Installa gli aggiornamenti nel dispositivo |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Elenca o ottiene i processi |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Elenca o ottiene le impostazioni di rete dei dispositivi |
> | Azione | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Elenca o ottiene i nodi |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Elenca o ottiene lo stato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Elimina gli ordini |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Elenca o ottiene gli ordini |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Elenca o ottiene gli ordini |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Crea o aggiorna gli ordini |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Elenca o ottiene i dispositivi Data Box Edge |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Elenca o ottiene i dispositivi Data Box Edge |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Elenca o ottiene i dispositivi Data Box Edge |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Elimina i ruoli |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Elenca o ottiene i ruoli |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Elenca o ottiene i ruoli |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Crea o aggiorna i ruoli |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Verifica la disponibilità di aggiornamenti |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Aggiorna le impostazioni di sicurezza |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Elimina le condivisioni |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Elenca o ottiene le condivisioni |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Elenca o ottiene le condivisioni |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Aggiornare i metadati della condivisione con i dati dal cloud |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Crea o aggiorna le condivisioni |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Elimina le credenziali dell'account di archiviazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Elenca o ottiene le credenziali dell'account di archiviazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Elenca o ottiene le credenziali dell'account di archiviazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Crea o aggiorna le credenziali dell'account di archiviazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Elimina i trigger |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Elenca o ottiene i trigger |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Elenca o ottiene i trigger |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Crea o aggiorna i trigger |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Elenca o ottiene il riepilogo aggiornamenti |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Carica il certificato per la registrazione dei dispositivi |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Elimina gli utenti della condivisione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Elenca o ottiene il risultato dell'operazione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Elenca o ottiene gli utenti della condivisione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Elenca o ottiene gli utenti della condivisione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Crea o aggiorna gli utenti della condivisione |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Crea o aggiorna i dispositivi Data Box Edge |
> | Azione | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Crea o aggiorna i dispositivi Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Databricks/locations/getNetworkPolicies/action | Ottenere criteri per finalità di rete per una subnet in base alla località usata da NRP |
> | Azione | Microsoft. databricks/locations/operationstatuses/Read | Legge lo stato dell'operazione per la risorsa. |
> | Azione | Microsoft. databricks/operazioni/lettura | Recupera l'elenco delle operazioni. |
> | Azione | Microsoft.Databricks/register/action | Consente di effettuare la registrazione a Databricks. |
> | Azione | Microsoft.Databricks/workspaces/delete | Rimuove un'area di lavoro di Databricks. |
> | Azione | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Regola le impostazioni di diagnostica disponibili per l'area di lavoro di Databricks |
> | Azione | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Aggiunge o modifica le impostazioni di diagnostica. |
> | Azione | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Ottiene le definizioni di log disponibili per l’area di lavoro Databricks |
> | Azione | Microsoft.Databricks/workspaces/read | Recupera un elenco di aree di lavoro di Databricks. |
> | Azione | Microsoft. databricks/area di lavoro/refreshPermissions/azione | Aggiornare le autorizzazioni per un'area di lavoro |
> | Azione | Microsoft. databricks/area di lavoro/updateDenyAssignment/azione | Aggiornare l'assegnazione di negazione non azioni per un gruppo di risorse gestite di un'area di lavoro |
> | Azione | Microsoft. databricks/Workspaces/virtualNetworkPeerings/Delete | Elimina un peering della rete virtuale |
> | Azione | Microsoft. databricks/area di lavoro/virtualNetworkPeerings/lettura | Ottiene il peering di rete virtuale. |
> | Azione | Microsoft. databricks/Workspaces/virtualNetworkPeerings/Write | Aggiungere o modificare il peering di rete virtuale |
> | Azione | Microsoft.Databricks/workspaces/write | Crea un'area di lavoro di Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataCatalog/catalogs/delete | Elimina la risorsa cataloghi per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/catalogs/read | Leggere la risorsa cataloghi per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/catalogs/write | Scrivere la risorsa cataloghi per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/checkNameAvailability/read | Controllare la disponibilità del nome catalogo per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/datacatalogs/delete | Elimina la risorsa datacatalog per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/datacatalogs/read | Leggere la risorsa datacatalog per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/datacatalogs/write | Scrivere la risorsa datacatalog per Data Catalog provider di risorse. |
> | Azione | Microsoft.DataCatalog/operations/read | Legge tutte le operazioni disponibili nel provider di risorse Data Catalog. |
> | Azione | Microsoft.DataCatalog/register/action | Registrare la sottoscrizione per il provider di risorse Data Catalog |
> | Azione | Microsoft.DataCatalog/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse Data Catalog |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Controlla se il nome della data factory è disponibile per l'uso. |
> | Azione | Microsoft.DataFactory/datafactories/activitywindows/read | Legge le finestre attività nella data factory con i parametri specificati. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Legge le finestre attività per l'attività della pipeline con i parametri specificati. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Legge le finestre attività per la pipeline con i parametri specificati. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/delete | Elimina tutte le pipeline. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Sospende tutte le pipeline. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/read | Legge tutte le pipeline. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Riprende tutte le pipeline. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/update/action | Aggiorna tutte le pipeline. |
> | Azione | Microsoft.DataFactory/datafactories/datapipelines/write | Crea o aggiorna tutte le pipeline. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Legge le finestre attività per il set di dati con i parametri specificati. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/delete | Elimina tutti i set di dati. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/read | Legge tutti i set di dati. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Legge l'esecuzione della sezione dati per il set di dati specificato con l'ora di inizio indicata. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/slices/read | Recupera le sezioni dati nel periodo specificato. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/slices/write | Aggiorna lo stato della sezione dati. |
> | Azione | Microsoft.DataFactory/datafactories/datasets/write | Crea o aggiorna tutti i set di dati. |
> | Azione | Microsoft.DataFactory/datafactories/delete | Elimina la data factory. |
> | Azione | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Legge le informazioni di connessione per tutti i gateway. |
> | Azione | Microsoft.DataFactory/datafactories/gateways/delete | Elimina tutti i gateway. |
> | Azione | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Elenca le chiavi di autenticazione per tutti i gateway. |
> | Azione | Microsoft.DataFactory/datafactories/gateways/read | Legge tutti i gateway. |
> | Azione | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Rigenera le chiavi di autenticazione per tutti i gateway. |
> | Azione | Microsoft.DataFactory/datafactories/gateways/write | Crea o aggiorna tutti i gateway. |
> | Azione | Microsoft.DataFactory/datafactories/linkedServices/delete | Elimina tutti i servizi collegati. |
> | Azione | Microsoft.DataFactory/datafactories/linkedServices/read | Legge tutti i servizi collegati. |
> | Azione | Microsoft.DataFactory/datafactories/linkedServices/write | Crea o aggiorna tutti i servizi collegati. |
> | Azione | Microsoft.DataFactory/datafactories/read | Legge la data factory. |
> | Azione | Microsoft.DataFactory/datafactories/runs/loginfo/read | Legge un URI di firma di accesso condiviso di un contenitore BLOB contenente i log. |
> | Azione | Microsoft.DataFactory/datafactories/tables/delete | Elimina tutti i set di dati. |
> | Azione | Microsoft.DataFactory/datafactories/tables/read | Legge tutti i set di dati. |
> | Azione | Microsoft.DataFactory/datafactories/tables/write | Crea o aggiorna tutti i set di dati. |
> | Azione | Microsoft.DataFactory/datafactories/write | Crea o aggiorna la data factory. |
> | Azione | Microsoft. DataFactory/Factory/addDataFlowToDebugSession/azione | Aggiungere il flusso di dati alla sessione di debug per l'anteprima. |
> | Azione | Microsoft.DataFactory/factories/cancelpipelinerun/action | Annulla l'esecuzione della pipeline specificata dall'ID esecuzione. |
> | Azione | Microsoft. DataFactory/Factory/cancelSandboxPipelineRun/azione | Annulla un'esecuzione del debug per la pipeline. |
> | Azione | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Crea una sessione di debug del flusso di dati. |
> | Azione | Microsoft.DataFactory/factories/dataflows/delete | Elimina il flusso di dati. |
> | Azione | Microsoft.DataFactory/factories/dataflows/read | Legge il flusso di dati. |
> | Azione | Microsoft.DataFactory/factories/dataflows/write | Crea o aggiorna flusso di dati |
> | Azione | Microsoft.DataFactory/factories/datasets/delete | Elimina tutti i set di dati. |
> | Azione | Microsoft.DataFactory/factories/datasets/read | Legge tutti i set di dati. |
> | Azione | Microsoft.DataFactory/factories/datasets/write | Crea o aggiorna tutti i set di dati. |
> | Azione | Microsoft. DataFactory/Factory/debugpipelineruns/Annulla/azione | Annulla un'esecuzione del debug per la pipeline. |
> | Azione | Microsoft.DataFactory/factories/delete | Elimina il data factory. |
> | Azione | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Elimina una sessione di debug del flusso di dati. |
> | Azione | Microsoft. DataFactory/Factory/executeDataFlowDebugCommand/azione | Comando Esegui debug flusso di dati. |
> | Azione | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Ottiene l'accesso al servizio Azure Data Factory DataPlane. |
> | Azione | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Legge l'accesso al servizio Azure Data Factory DataPlane. |
> | Azione | Microsoft.DataFactory/factories/getFeatureValue/action | Ottiene il valore della funzionalità di controllo dell'esposizione per la località specifica. |
> | Azione | Microsoft.DataFactory/factories/getFeatureValue/read | Legge il valore della funzionalità di controllo dell'esposizione per la località specifica. |
> | Azione | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Ottiene il token di accesso GitHub. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/delete | Elimina tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Legge le informazioni di connessione dei runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Ottiene i metadati del runtime di integrazione SSIS per il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Legge lo stato dei runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Crea il riferimento al runtime di integrazione collegato nel runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Elenca le chiavi di autenticazione per tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Recupera i dati di monitoraggio per tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Elimina il nodo per il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Restituisce l'indirizzo IP per il nodo specificato del runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Legge il nodo per il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Aggiorna un nodo di runtime di integrazione self-hosted. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/read | Legge tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Aggiorna i metadati del runtime di integrazione SSIS per il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Rigenera le chiavi di autenticazione per il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Rimuove i riferimenti del runtime di integrazione collegato dal runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/start/action | Avvia tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Arresta tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincronizza le credenziali per il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Aggiorna il runtime di integrazione specificato. |
> | Azione | Microsoft.DataFactory/factories/integrationruntimes/write | Crea o aggiorna tutti i runtime di integrazione. |
> | Azione | Microsoft.DataFactory/factories/linkedServices/delete | Elimina il servizio collegato. |
> | Azione | Microsoft.DataFactory/factories/linkedServices/read | Legge il servizio collegato. |
> | Azione | Microsoft.DataFactory/factories/linkedServices/write | Crea o aggiorna il servizio collegato. |
> | Azione | Microsoft. DataFactory/Factory/operationResults/lettura | Ottiene i risultati dell'operazione. |
> | Azione | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Legge le esecuzioni di attività per l'ID di esecuzione pipeline specificato. |
> | Azione | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Annulla l'esecuzione della pipeline specificata dall'ID esecuzione. |
> | Azione | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Esegue query sulle esecuzioni di attività per l'ID di esecuzione pipeline specificato. |
> | Azione | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Legge i risultati di attività di query per l'ID di esecuzione pipeline specificato. |
> | Azione | Microsoft.DataFactory/factories/pipelineruns/read | Legge le esecuzioni di pipeline. |
> | Azione | Microsoft.DataFactory/factories/pipelines/createrun/action | Crea un'esecuzione per la pipeline. |
> | Azione | Microsoft.DataFactory/factories/pipelines/delete | Elimina la pipeline. |
> | Azione | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Ottiene lo stato di avanzamento di Esecuzioni attività. |
> | Azione | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Legge l'esecuzione della pipeline. |
> | Azione | Microsoft.DataFactory/factories/pipelines/read | Esegue la lettura della pipeline. |
> | Azione | Microsoft. DataFactory/Factory/pipeline/sandbox/azione | Crea un ambiente di esecuzione del debug per la pipeline. |
> | Azione | Microsoft. DataFactory/Factory/pipeline/sandbox/creazione/azione | Crea un ambiente di esecuzione del debug per la pipeline. |
> | Azione | Microsoft. DataFactory/Factory/pipeline/sandbox/esecuzione/azione | Crea un'esecuzione del debug per la pipeline. |
> | Azione | Microsoft.DataFactory/factories/pipelines/write | Crea o aggiorna la pipeline |
> | Azione | Microsoft. DataFactory/Factory/querydataflowdebugsessions/azione | Esegue una query su una sessione di debug del flusso di dati |
> | Azione | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Recupera le esecuzioni della pipeline di debug. |
> | Azione | Microsoft.DataFactory/factories/querypipelineruns/action | Esegue query sull'esecuzione della pipeline. |
> | Azione | Microsoft.DataFactory/factories/querypipelineruns/read | Legge il risultato di Esecuzioni di pipeline di query. |
> | Azione | Microsoft.DataFactory/factories/querytriggerruns/action | Esegue query sulle esecuzioni di trigger. |
> | Azione | Microsoft.DataFactory/factories/querytriggerruns/read | Legge il risultato di Esecuzioni di trigger. |
> | Azione | Microsoft.DataFactory/factories/read | Esegue la lettura del data factory. |
> | Azione | Microsoft. DataFactory/Factory/sandboxpipelineruns/azione | Recupera le esecuzioni della pipeline di debug. |
> | Azione | Microsoft. DataFactory/Factory/sandboxpipelineruns/lettura | Ottiene le informazioni sull'esecuzione del debug per la pipeline. |
> | Azione | Microsoft. DataFactory/Factory/sandboxpipelineruns/sandboxActivityRuns/lettura | Ottiene le informazioni sull'esecuzione del debug per l'attività. |
> | Azione | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Avvia una sessione di debug del flusso di dati. |
> | Azione | Microsoft.DataFactory/factories/triggerruns/read | Legge le esecuzioni di trigger. |
> | Azione | Microsoft.DataFactory/factories/triggers/delete | Elimina tutti i trigger. |
> | Azione | Microsoft. DataFactory/Factory/Triggers/geteventsubscriptionstatus/Action | Stato della sottoscrizione di eventi. |
> | Azione | Microsoft.DataFactory/factories/triggers/read | Legge tutti i trigger. |
> | Azione | Microsoft.DataFactory/factories/triggers/start/action | Avvia tutti i trigger. |
> | Azione | Microsoft.DataFactory/factories/triggers/stop/action | Arresta tutti i trigger. |
> | Azione | Microsoft. DataFactory/Factory/Triggers/SubscribeToEvents/Action | Sottoscrivere gli eventi. |
> | Azione | Microsoft.DataFactory/factories/triggers/triggerruns/read | Legge le esecuzioni di trigger. |
> | Azione | Microsoft. DataFactory/Factory/Triggers/UnsubscribeFromEvents/Action | Annulla la sottoscrizione agli eventi. |
> | Azione | Microsoft.DataFactory/factories/triggers/write | Crea o aggiorna tutti i trigger. |
> | Azione | Microsoft.DataFactory/factories/write | Crea o aggiorna il data factory |
> | Azione | Microsoft.DataFactory/locations/configureFactoryRepo/action | Consente di configurare il repository per la factory. |
> | Azione | Microsoft.DataFactory/locations/getFeatureValue/action | Ottiene il valore della funzionalità di controllo dell'esposizione per la località specifica. |
> | Azione | Microsoft.DataFactory/locations/getFeatureValue/read | Legge il valore della funzionalità di controllo dell'esposizione per la località specifica. |
> | Azione | Microsoft.DataFactory/operations/read | Legge tutte le operazioni in Microsoft Data Factory Provider. |
> | Azione | Microsoft.DataFactory/register/action | Registra la sottoscrizione per il provider di risorse di Data Factory. |
> | Azione | Microsoft.DataFactory/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse di Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Elimina criteri di calcolo. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Ottiene informazioni su criteri di calcolo. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Crea o aggiorna criteri di calcolo. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Scollega un account Data Lake Store da un account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Ottiene informazioni su un account Archivio Data Lake collegato a un account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Crea o aggiorna un account Data Lake Store collegato a un account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/delete | Elimina un account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Elimina una regola del firewall. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Ottiene informazioni su una regola del firewall. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Crea o aggiorna una regola del firewall. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Ottiene i risultati di un'operazione di account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/read | Ottiene informazioni su un account Analisi Data Lake esistente. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Elenca i token SAS per i contenitori di archiviazione di un account di archiviazione collegato a un account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Ottiene i contenitori di un account di archiviazione collegato a un account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Scollega un account di archiviazione da un account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Ottiene informazioni su un account di archiviazione collegato a un account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Crea o aggiorna un account di archiviazione collegato a un account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede le autorizzazioni per annullare i processi inviati da altri utenti. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Trasferisce SystemMaxAnalyticsUnits tra account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Eliminare una regola della rete virtuale. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Ottenere informazioni su una regola della rete virtuale. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Creare o aggiornare una regola della rete virtuale. |
> | Azione | Microsoft.DataLakeAnalytics/accounts/write | Crea o aggiorna un account Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/locations/capability/read | Ottiene informazioni sulla capacità di una sottoscrizione sull'utilizzo di Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Controlla la disponibilità di un nome di account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/locations/operationResults/read | Ottiene i risultati di un'operazione di account Analisi Data Lake. |
> | Azione | Microsoft.DataLakeAnalytics/locations/usages/read | Ottiene informazioni sull'utilizzo delle quote di una sottoscrizione relativamente all'uso di Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/operations/read | Ottiene le operazioni disponibili di Data Lake Analytics. |
> | Azione | Microsoft.DataLakeAnalytics/register/action | Registra la sottoscrizione a Data Lake Analytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataLakeStore/accounts/delete | Elimina un account Archivio Data Lake. |
> | Azione | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Abilita KeyVault per un account Archivio Data Lake. |
> | Azione | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Elimina un filtro EventGrid. |
> | Azione | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Ottiene un filtro EventGrid. |
> | Azione | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Crea o aggiorna un filtro EventGrid. |
> | Azione | Microsoft.DataLakeStore/accounts/firewallRules/delete | Elimina una regola del firewall. |
> | Azione | Microsoft.DataLakeStore/accounts/firewallRules/read | Ottiene informazioni su una regola del firewall. |
> | Azione | Microsoft.DataLakeStore/accounts/firewallRules/write | Crea o aggiorna una regola del firewall. |
> | Azione | Microsoft.DataLakeStore/accounts/operationResults/read | Ottiene i risultati di un'operazione di account Archivio Data Lake. |
> | Azione | Microsoft.DataLakeStore/accounts/read | Ottiene informazioni su un account Archivio Data Lake esistente. |
> | Azione | Microsoft. archivio data Lake/accounts/shares/Delete | Eliminare una condivisione. |
> | Azione | Microsoft. archivio data Lake/accounts/shares/Read | Ottenere informazioni su una condivisione. |
> | Azione | Microsoft. archivio data Lake/accounts/shares/Write | Crea o aggiorna una condivisione. |
> | Azione | Microsoft.DataLakeStore/accounts/Superuser/action | Concede privilegi avanzati a un utente quando si usa Microsoft.Authorization/roleAssignments/write. |
> | Azione | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Elimina un provider di identità attendibile. |
> | Azione | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Ottiene informazioni su un provider di identità attendibile. |
> | Azione | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Crea o aggiorna un provider di identità attendibile. |
> | Azione | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Eliminare una regola della rete virtuale. |
> | Azione | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Ottenere informazioni su una regola della rete virtuale. |
> | Azione | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Creare o aggiornare una regola della rete virtuale. |
> | Azione | Microsoft.DataLakeStore/accounts/write | Crea o aggiorna un account Archivio Data Lake. |
> | Azione | Microsoft.DataLakeStore/locations/capability/read | Ottiene informazioni sulla capacità di una sottoscrizione sull'utilizzo di Data Lake Store. |
> | Azione | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Controlla la disponibilità di un nome di account Archivio Data Lake. |
> | Azione | Microsoft.DataLakeStore/locations/operationResults/read | Ottiene i risultati di un'operazione di account Archivio Data Lake. |
> | Azione | Microsoft.DataLakeStore/locations/usages/read | Ottiene informazioni sull'utilizzo delle quote di una sottoscrizione relativamente all'uso di Data Lake Store. |
> | Azione | Microsoft.DataLakeStore/operations/read | Ottiene le operazioni disponibili di Data Lake Store. |
> | Azione | Microsoft.DataLakeStore/register/action | Registra la sottoscrizione a Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DataMigration/locations/operationResults/read | Recupera lo stato di un'operazione a esecuzione prolungata correlata a una risposta 202 - Accettato |
> | Azione | Microsoft.DataMigration/locations/operationStatuses/read | Recupera lo stato di un'operazione a esecuzione prolungata correlata a una risposta 202 - Accettato |
> | Azione | Microsoft.DataMigration/register/action | Registra la sottoscrizione con il provider del Servizio Migrazione del database di Azure |
> | Azione | Microsoft.DataMigration/services/addWorker/action | Aggiunge un thread di lavoro DMS ai WDS disponibile worker del servizio |
> | Azione | Microsoft.DataMigration/services/checkStatus/action | Controlla se il servizio è stato distribuito ed è in esecuzione |
> | Azione | Microsoft.DataMigration/services/configureWorker/action | Configura un thread di lavoro DMS per i WDS disponibile worker del servizio |
> | Azione | Microsoft.DataMigration/services/delete | Elimina una risorsa e tutti i relativi elementi figlio |
> | Azione | Microsoft.DataMigration/services/projects/accessArtifacts/action | Genera un URL da usare per le operazioni GET o PUT sugli elementi del progetto |
> | Azione | Microsoft.DataMigration/services/projects/delete | Elimina una risorsa e tutti i relativi elementi figlio |
> | Azione | Microsoft.DataMigration/services/projects/files/delete | Elimina una risorsa e tutti i relativi elementi figlio |
> | Azione | Microsoft.DataMigration/services/projects/files/read | Legge le informazioni sulle risorse |
> | Azione | Microsoft.DataMigration/services/projects/files/read/action | Ottiene un URL da potere usare per leggere il contenuto del file |
> | Azione | Microsoft.DataMigration/services/projects/files/readWrite/action | Ottiene un URL da potere usare per leggere o scrivere il contenuto del file |
> | Azione | Microsoft.DataMigration/services/projects/files/write | Crea o aggiorna le risorse e le relative proprietà |
> | Azione | Microsoft.DataMigration/services/projects/read | Legge le informazioni sulle risorse |
> | Azione | Microsoft.DataMigration/services/projects/tasks/cancel/action | Annulla l'attività se è attualmente in esecuzione |
> | Azione | Microsoft.DataMigration/services/projects/tasks/delete | Elimina una risorsa e tutti i relativi elementi figlio |
> | Azione | Microsoft.DataMigration/services/projects/tasks/read | Legge le informazioni sulle risorse |
> | Azione | Microsoft.DataMigration/services/projects/tasks/write | Esegue le attività del Servizio Migrazione del database di Azure |
> | Azione | Microsoft.DataMigration/services/projects/write | Esegue le attività del Servizio Migrazione del database di Azure |
> | Azione | Microsoft.DataMigration/services/read | Legge le informazioni sulle risorse |
> | Azione | Microsoft.DataMigration/services/removeWorker/action | Rimuove un thread di lavoro DMS ai WDS disponibile worker del servizio |
> | Azione | Microsoft.DataMigration/services/serviceTasks/cancel/action | Annulla l'attività se è attualmente in esecuzione |
> | Azione | Microsoft.DataMigration/services/serviceTasks/delete | Elimina una risorsa e tutti i relativi elementi figlio |
> | Azione | Microsoft.DataMigration/services/serviceTasks/read | Legge le informazioni sulle risorse |
> | Azione | Microsoft.DataMigration/services/serviceTasks/write | Esegue le attività del Servizio Migrazione del database di Azure |
> | Azione | Microsoft.DataMigration/services/slots/delete | Elimina una risorsa e tutti i relativi elementi figlio |
> | Azione | Microsoft.DataMigration/services/slots/read | Legge le informazioni sulle risorse |
> | Azione | Microsoft.DataMigration/services/slots/write | Crea o aggiorna le risorse e le relative proprietà |
> | Azione | Microsoft.DataMigration/services/start/action | Avvia il Servizio Migrazione del database di Azure per consentirne di nuovo l'elaborazione delle migrazioni |
> | Azione | Microsoft.DataMigration/services/stop/action | Arresta il Servizio Migrazione del database di Azure per ridurne i costi |
> | Azione | Microsoft.DataMigration/services/updateAgentConfig/action | Aggiorna la configurazione dell'agente DMS con i valori specificati. |
> | Azione | Microsoft.DataMigration/services/write | Crea o aggiorna le risorse e le relative proprietà |
> | Azione | Microsoft.DataMigration/skus/read | Ottiene un elenco di SKU supportati dalle risorse del Servizio Migrazione del database di Azure. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DBforMariaDB/checkNameAvailability/action | Verifica se un determinato nome server è disponibile per il provisioning in tutto il mondo per una determinata sottoscrizione. |
> | Azione | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Restituisci risultati operazione server MariaDB |
> | Azione | Microsoft.DBforMariaDB/locations/operationResults/read | Restituisci risultati operazione server MariaDB basata su ResourceGroup |
> | Azione | Microsoft.DBforMariaDB/locations/operationResults/read | Restituisci risultati operazione server MariaDB |
> | Azione | Microsoft.DBforMariaDB/locations/performanceTiers/read | Restituisce l'elenco dei livelli di prestazioni disponibili. |
> | Azione | Microsoft. DBforMariaDB/locations/privateEndpointConnectionAzureAsyncOperation/Read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft. DBforMariaDB/locations/privateEndpointConnectionOperationResults/Read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft. DBforMariaDB/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft. DBforMariaDB/locations/privateEndpointConnectionProxyOperationResults/Read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Restituisce l'elenco dei risultati dell'operazione di rilevamento minacce server. |
> | Azione | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Restituisce l'elenco dei risultati dell'operazione di rilevamento minacce server. |
> | Azione | Microsoft. DBforMariaDB/locations/serverKeyAzureAsyncOperation/Read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft. DBforMariaDB/locations/serverKeyOperationResults/Read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft.DBforMariaDB/operations/read | Restituisce l'elenco di operazioni MariaDB. |
> | Azione | Microsoft.DBforMariaDB/performanceTiers/read | Restituisce l'elenco dei livelli di prestazioni disponibili. |
> | Azione | Microsoft.DBforMariaDB/register/action | Registrare il provider di risorse MariaDB |
> | Azione | Microsoft.DBforMariaDB/servers/administrators/delete | Elimina un amministratore esistente del server MariaDB. |
> | Azione | Microsoft.DBforMariaDB/servers/administrators/read | Ottiene un elenco di amministratori del server MariaDB. |
> | Azione | Microsoft.DBforMariaDB/servers/administrators/write | Crea o aggiorna l'amministratore del server MariaDB con i parametri specificati. |
> | Azione | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Crea una nuova sessione di azione consigliata |
> | Azione | Microsoft.DBforMariaDB/servers/advisors/read | Restituisce l'elenco degli advisor |
> | Azione | Microsoft.DBforMariaDB/servers/advisors/read | Restituisce un Advisor |
> | Azione | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Restituisce l'elenco delle azioni consigliate |
> | Azione | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Restituisce l'elenco delle azioni consigliate |
> | Azione | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Restituisce un'azione consigliata |
> | Azione | Microsoft.DBforMariaDB/servers/configurations/read | Restituisce l'elenco delle configurazioni per un server o ottiene le proprietà per la configurazione specificata. |
> | Azione | Microsoft.DBforMariaDB/servers/configurations/write | Aggiorna il valore per la configurazione specificata |
> | Azione | Microsoft.DBforMariaDB/servers/databases/delete | Elimina un database MariaDB esistente. |
> | Azione | Microsoft.DBforMariaDB/servers/databases/read | Restituisce l'elenco dei database MariaDB o ottiene le proprietà per il database specificato. |
> | Azione | Microsoft.DBforMariaDB/servers/databases/write | Crea un database MariaDB con i parametri specificati o aggiorna le proprietà per il database specificato. |
> | Azione | Microsoft.DBforMariaDB/servers/delete | Elimina un server esistente. |
> | Azione | Microsoft.DBforMariaDB/servers/firewallRules/delete | Elimina una regola firewall esistente. |
> | Azione | Microsoft.DBforMariaDB/servers/firewallRules/read | Restituisce l'elenco delle regole del firewall per un server o ottiene le proprietà per la regola del firewall specificata. |
> | Azione | Microsoft.DBforMariaDB/servers/firewallRules/write | Crea una regola del firewall con i parametri specificati o aggiorna una regola esistente. |
> | Azione | Microsoft. DBforMariaDB/Servers/Keys/Delete | Elimina una chiave server esistente. |
> | Azione | Microsoft. DBforMariaDB/server/chiavi/lettura | Restituisce l'elenco delle chiavi server o ottiene le proprietà per la chiave server specificata. |
> | Azione | Microsoft. DBforMariaDB/server/chiavi/scrittura | Crea una chiave con i parametri specificati o aggiorna le proprietà o i tag per la chiave server specificata. |
> | Azione | Microsoft.DBforMariaDB/servers/logFiles/read | Restituisce l'elenco dei file di log MariaDB. |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Delete | Elimina un proxy di connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Read | Restituisce l'elenco dei proxy di connessione all'endpoint privato o ottiene le proprietà per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Validate/Action | Convalida una chiamata di creazione di una connessione all'endpoint privato dal lato NRP |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Write | Crea un proxy di connessione all'endpoint privato con i parametri specificati o aggiorna le proprietà o i tag per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Delete | Elimina una connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Read | Restituisce l'elenco delle connessioni all'endpoint privato o ottiene le proprietà per la connessione all'endpoint privato specificata. |
> | Azione | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Write | Approva o rifiuta una connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforMariaDB/Servers/privateLinkResources/Read | Ottenere le risorse di collegamento privato per il server MariaDB corrispondente |
> | Azione | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Ottiene i log disponibili per i server MariaDB |
> | Azione | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i database |
> | Azione | Microsoft.DBforMariaDB/servers/queryTexts/action | Restituisce i testi per un elenco di query |
> | Azione | Microsoft.DBforMariaDB/servers/queryTexts/action | Restituisce il testo di una query |
> | Azione | Microsoft.DBforMariaDB/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Azione | Microsoft.DBforMariaDB/servers/recoverableServers/read | Restituisce le informazioni recuperabili del server MariaDB |
> | Azione | Microsoft.DBforMariaDB/servers/replicas/read | Ottenere le repliche di lettura di un server MariaDB |
> | Azione | Microsoft.DBforMariaDB/servers/restart/action | Riavvia un server specifico. |
> | Azione | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Recupera i dettagli dei criteri di rilevamento minacce del server configurati in uno specifico server |
> | Azione | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce del server per uno specifico server |
> | Azione | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Restituisce l'elenco delle statistiche delle query per le query più frequenti. |
> | Azione | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Restituisce una statistica della query |
> | Azione | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Aggiorna le configurazioni per il server specificato |
> | Azione | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Elimina una regola di rete virtuale esistente |
> | Azione | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata. |
> | Azione | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata. |
> | Azione | Microsoft.DBforMariaDB/servers/waitStatistics/read | Restituisce le statistiche di attesa per un'istanza |
> | Azione | Microsoft.DBforMariaDB/servers/waitStatistics/read | Restituisce una statistica di attesa |
> | Azione | Microsoft.DBforMariaDB/servers/write | Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DBforMySQL/checkNameAvailability/action | Verifica se un determinato nome server è disponibile per il provisioning in tutto il mondo per una determinata sottoscrizione. |
> | Azione | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Restituisci risultati operazione server MySQL |
> | Azione | Microsoft.DBforMySQL/locations/operationResults/read | Restituisce i risultati dell'operazione server MySQL basata su ResourceGroup |
> | Azione | Microsoft.DBforMySQL/locations/operationResults/read | Restituisci risultati operazione server MySQL |
> | Azione | Microsoft.DBforMySQL/locations/performanceTiers/read | Restituisce l'elenco dei livelli di prestazioni disponibili. |
> | Azione | Microsoft. DBforMySQL/locations/privateEndpointConnectionAzureAsyncOperation/Read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft. DBforMySQL/locations/privateEndpointConnectionOperationResults/Read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft. DBforMySQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft. DBforMySQL/locations/privateEndpointConnectionProxyOperationResults/Read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Restituisce l'elenco dei risultati dell'operazione di rilevamento minacce server. |
> | Azione | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Restituisce l'elenco dei risultati dell'operazione di rilevamento minacce server. |
> | Azione | Microsoft. DBforMySQL/locations/serverKeyAzureAsyncOperation/Read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft. DBforMySQL/locations/serverKeyOperationResults/Read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft.DBforMySQL/operations/read | Restituisce l'elenco delle operazioni di MySQL. |
> | Azione | Microsoft.DBforMySQL/performanceTiers/read | Restituisce l'elenco dei livelli di prestazioni disponibili. |
> | Azione | Microsoft.DBforMySQL/register/action | Registrare il provider di risorse MySQL |
> | Azione | Microsoft.DBforMySQL/servers/administrators/delete | Elimina un amministratore esistente del server MySQL. |
> | Azione | Microsoft.DBforMySQL/servers/administrators/read | Ottiene un elenco di amministratori del server MySQL. |
> | Azione | Microsoft.DBforMySQL/servers/administrators/write | Crea o aggiorna l'amministratore del server MySQL con i parametri specificati. |
> | Azione | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Crea una nuova sessione di azione consigliata |
> | Azione | Microsoft.DBforMySQL/servers/advisors/read | Restituisce l'elenco degli advisor |
> | Azione | Microsoft.DBforMySQL/servers/advisors/read | Restituisce un Advisor |
> | Azione | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Restituisce l'elenco delle azioni consigliate |
> | Azione | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Restituisce l'elenco delle azioni consigliate |
> | Azione | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Restituisce un'azione consigliata |
> | Azione | Microsoft.DBforMySQL/servers/configurations/read | Restituisce l'elenco delle configurazioni per un server o ottiene le proprietà per la configurazione specificata. |
> | Azione | Microsoft.DBforMySQL/servers/configurations/write | Aggiorna il valore per la configurazione specificata |
> | Azione | Microsoft.DBforMySQL/servers/databases/delete | Elimina un database MySQL esistente. |
> | Azione | Microsoft.DBforMySQL/servers/databases/read | Restituisce l'elenco dei database MySQL o ottiene le proprietà per il database specificato. |
> | Azione | Microsoft.DBforMySQL/servers/databases/write | Crea un database MySQL con i parametri specificati o aggiorna le proprietà per il database specificato. |
> | Azione | Microsoft.DBforMySQL/servers/delete | Elimina un server esistente. |
> | Azione | Microsoft.DBforMySQL/servers/firewallRules/delete | Elimina una regola firewall esistente. |
> | Azione | Microsoft.DBforMySQL/servers/firewallRules/read | Restituisce l'elenco delle regole del firewall per un server o ottiene le proprietà per la regola del firewall specificata. |
> | Azione | Microsoft.DBforMySQL/servers/firewallRules/write | Crea una regola del firewall con i parametri specificati o aggiorna una regola esistente. |
> | Azione | Microsoft. DBforMySQL/Servers/Keys/Delete | Elimina una chiave server esistente. |
> | Azione | Microsoft. DBforMySQL/server/chiavi/lettura | Restituisce l'elenco delle chiavi server o ottiene le proprietà per la chiave server specificata. |
> | Azione | Microsoft. DBforMySQL/server/chiavi/scrittura | Crea una chiave con i parametri specificati o aggiorna le proprietà o i tag per la chiave server specificata. |
> | Azione | Microsoft.DBforMySQL/servers/logFiles/read | Restituisce l'elenco dei file di log di MySQL. |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Delete | Elimina un proxy di connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Read | Restituisce l'elenco dei proxy di connessione all'endpoint privato o ottiene le proprietà per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Validate/Action | Convalida una chiamata di creazione di una connessione all'endpoint privato dal lato NRP |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Write | Crea un proxy di connessione all'endpoint privato con i parametri specificati o aggiorna le proprietà o i tag per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Delete | Elimina una connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Read | Restituisce l'elenco delle connessioni all'endpoint privato o ottiene le proprietà per la connessione all'endpoint privato specificata. |
> | Azione | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Write | Approva o rifiuta una connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforMySQL/Servers/privateLinkResources/Read | Ottenere le risorse di collegamento privato per il server MySQL corrispondente |
> | Azione | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Ottiene i log disponibili per i server MySQL |
> | Azione | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i database |
> | Azione | Microsoft.DBforMySQL/servers/queryTexts/action | Restituisce i testi per un elenco di query |
> | Azione | Microsoft.DBforMySQL/servers/queryTexts/action | Restituisce il testo di una query |
> | Azione | Microsoft.DBforMySQL/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Azione | Microsoft.DBforMySQL/servers/recoverableServers/read | Restituisce le informazioni recuperabili del server MySQL |
> | Azione | Microsoft.DBforMySQL/servers/replicas/read | Ottenere le repliche di lettura di un server MySQL |
> | Azione | Microsoft.DBforMySQL/servers/restart/action | Riavvia un server specifico. |
> | Azione | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Recupera i dettagli dei criteri di rilevamento minacce del server configurati in uno specifico server |
> | Azione | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce del server per uno specifico server |
> | Azione | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Restituisce l'elenco delle statistiche delle query per le query più frequenti. |
> | Azione | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Restituisce una statistica della query |
> | Azione | Microsoft.DBforMySQL/servers/updateConfigurations/action | Aggiorna le configurazioni per il server specificato |
> | Azione | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Elimina una regola di rete virtuale esistente |
> | Azione | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata. |
> | Azione | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata. |
> | Azione | Microsoft.DBforMySQL/servers/waitStatistics/read | Restituisce le statistiche di attesa per un'istanza |
> | Azione | Microsoft.DBforMySQL/servers/waitStatistics/read | Restituisce una statistica di attesa |
> | Azione | Microsoft.DBforMySQL/servers/write | Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verifica se un determinato nome server è disponibile per il provisioning in tutto il mondo per una determinata sottoscrizione. |
> | Azione | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Restituisci risultati operazione server PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/locations/operationResults/read | Restituisci risultati operazione server PostgreSQL basati su ResourceGroup |
> | Azione | Microsoft.DBforPostgreSQL/locations/operationResults/read | Restituisci risultati operazione server PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Restituisce l'elenco dei livelli di prestazioni disponibili. |
> | Azione | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionAzureAsyncOperation/Read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionOperationResults/Read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionProxyOperationResults/Read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Restituisce l'elenco dei risultati dell'operazione di rilevamento minacce server. |
> | Azione | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Restituisce l'elenco dei risultati dell'operazione di rilevamento minacce server. |
> | Azione | Microsoft. DBforPostgreSQL/locations/serverKeyAzureAsyncOperation/Read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft. DBforPostgreSQL/locations/serverKeyOperationResults/Read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft.DBforPostgreSQL/operations/read | Restituisce l'elenco delle operazioni di PostgreSQL. |
> | Azione | Microsoft.DBforPostgreSQL/performanceTiers/read | Restituisce l'elenco dei livelli di prestazioni disponibili. |
> | Azione | Microsoft.DBforPostgreSQL/register/action | Registrare il provider di risorse PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/servers/administrators/delete | Elimina un amministratore esistente del server PostgreSQL. |
> | Azione | Microsoft.DBforPostgreSQL/servers/administrators/read | Ottiene un elenco di amministratori del server PostgreSQL. |
> | Azione | Microsoft.DBforPostgreSQL/servers/administrators/write | Crea o aggiorna l'amministratore del server PostgreSQL con i parametri specificati. |
> | Azione | Microsoft.DBforPostgreSQL/servers/advisors/read | Restituisce l'elenco degli advisor |
> | Azione | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Restituisce l'elenco delle azioni consigliate |
> | Azione | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Crea i consigli |
> | Azione | Microsoft.DBforPostgreSQL/servers/configurations/read | Restituisce l'elenco delle configurazioni per un server o ottiene le proprietà per la configurazione specificata. |
> | Azione | Microsoft.DBforPostgreSQL/servers/configurations/write | Aggiorna il valore per la configurazione specificata |
> | Azione | Microsoft.DBforPostgreSQL/servers/databases/delete | Elimina un database PostgreSQL esistente. |
> | Azione | Microsoft.DBforPostgreSQL/servers/databases/read | Restituisce l'elenco dei database PostgreSQL o ottiene le proprietà per il database specificato. |
> | Azione | Microsoft.DBforPostgreSQL/servers/databases/write | Crea un database PostgreSQL con i parametri specificati o aggiorna le proprietà per il database specificato. |
> | Azione | Microsoft.DBforPostgreSQL/servers/delete | Elimina un server esistente. |
> | Azione | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Elimina una regola firewall esistente. |
> | Azione | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Restituisce l'elenco delle regole del firewall per un server o ottiene le proprietà per la regola del firewall specificata. |
> | Azione | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Crea una regola del firewall con i parametri specificati o aggiorna una regola esistente. |
> | Azione | Microsoft. DBforPostgreSQL/Servers/Keys/Delete | Elimina una chiave server esistente. |
> | Azione | Microsoft. DBforPostgreSQL/server/chiavi/lettura | Restituisce l'elenco delle chiavi server o ottiene le proprietà per la chiave server specificata. |
> | Azione | Microsoft. DBforPostgreSQL/server/chiavi/scrittura | Crea una chiave con i parametri specificati o aggiorna le proprietà o i tag per la chiave server specificata. |
> | Azione | Microsoft.DBforPostgreSQL/servers/logFiles/read | Restituisce l'elenco dei file di log di PostgreSQL. |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Delete | Elimina un proxy di connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Read | Restituisce l'elenco dei proxy di connessione all'endpoint privato o ottiene le proprietà per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Convalida una chiamata di creazione di una connessione all'endpoint privato dal lato NRP |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Crea un proxy di connessione all'endpoint privato con i parametri specificati o aggiorna le proprietà o i tag per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Delete | Elimina una connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Read | Restituisce l'elenco delle connessioni all'endpoint privato o ottiene le proprietà per la connessione all'endpoint privato specificata. |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Approva o rifiuta una connessione all'endpoint privato esistente |
> | Azione | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/Read | Ottenere le risorse di collegamento privato per il server PostgreSQL corrispondente |
> | Azione | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Recupera i log disponibili per i server PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i database |
> | Azione | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Restituisce il testo di una query |
> | Azione | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Restituisce i testi per un elenco di query |
> | Azione | Microsoft.DBforPostgreSQL/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Azione | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Restituisce le informazioni recuperabili del server PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/servers/replicas/read | Ottenere le repliche di lettura di un server PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/servers/restart/action | Riavvia un server specifico. |
> | Azione | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Recupera i dettagli dei criteri di rilevamento minacce del server configurati in uno specifico server |
> | Azione | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce del server per uno specifico server |
> | Azione | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Restituisce l'elenco delle statistiche delle query per le query più frequenti. |
> | Azione | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Aggiorna le configurazioni per il server specificato |
> | Azione | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Elimina una regola di rete virtuale esistente |
> | Azione | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata. |
> | Azione | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata. |
> | Azione | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Restituisce le statistiche di attesa per un'istanza |
> | Azione | Microsoft.DBforPostgreSQL/servers/write | Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato. |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Restituisce l'elenco delle configurazioni per un server o ottiene le proprietà per la configurazione specificata. |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Aggiorna il valore per la configurazione specificata |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/delete | Elimina un server esistente. |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Elimina una regola firewall esistente. |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Restituisce l'elenco delle regole del firewall per un server o ottiene le proprietà per la regola del firewall specificata. |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Crea una regola del firewall con i parametri specificati o aggiorna una regola esistente. |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Recupera i log disponibili per i server PostgreSQL |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i database |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Aggiorna le configurazioni per il server specificato |
> | Azione | Microsoft.DBforPostgreSQL/serversv2/write | Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. Devices/account/diagnosticSettings/Read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft. Devices/account/diagnosticSettings/Write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft. Devices/account/logDefinitions/Read | Ottiene le definizioni di log disponibili per il servizio IotHub |
> | Azione | Microsoft. Devices/account/metricDefinitions/Read | Ottiene la metrica disponibile per il servizio IotHub |
> | Azione | Microsoft.Devices/checkNameAvailability/Action | Controlla se il nome IotHub è disponibile |
> | Azione | Microsoft.Devices/checkNameAvailability/Action | Controlla se il nome IotHub è disponibile |
> | Azione | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Controlla se il nome del servizio di provisioning è disponibile |
> | Azione | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Controlla se il nome del servizio di provisioning è disponibile |
> | Azione | Microsoft. Devices/digitalTwins/Delete | Elimina un account Digital Twins esistente e tutti i relativi elementi figlio |
> | Azione | Microsoft. Devices/digitalTwins/operationresults/Read | Ottenere lo stato di un'operazione su un account di dispositivi gemelli digitali |
> | Azione | Microsoft. Devices/digitalTwins/Read | Ottiene un elenco degli account gemelli digitali associati a una sottoscrizione |
> | Azione | Microsoft. Devices/digitalTwins/SKU/Read | Ottenere un elenco degli SKU validi per gli account dei dispositivi gemelli digitali |
> | Azione | Microsoft. Devices/digitalTwins/Write | Creare un nuovo account digitial Twins |
> | Azione | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft. Devices/elasticPools/eventGridFilters/Delete | Elimina il filtro di griglia di eventi Pool elastico |
> | Azione | Microsoft. Devices/elasticPools/eventGridFilters/Read | Ottiene il filtro di griglia di eventi Pool elastico |
> | Azione | Microsoft. Devices/elasticPools/eventGridFilters/Write | Crea nuovo o aggiorna il filtro di griglia di eventi Pool elastico esistente |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Elimina il certificato |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Genera il codice di verifica |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Recupera il certificato |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifica la risorsa certificato |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Crea o aggiorna il certificato |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Elimina la risorsa tenant IotHub |
> | Azione | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Elimina un gruppo di consumer EventHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Ottiene il gruppo o i gruppi di consumer EventHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Crea un gruppo di consumer EventHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Esporta dispositivi |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Ottiene la risorsa di statistiche del tenant IotHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importa dispositivi |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Ottiene la chiave tenant IotHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Ottiene i dettagli del processo o dei processi inviati per un servizio IotHub specifico |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Ottiene le chiavi tenant IotHub |
> | Azione | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Ottiene le definizioni di log disponibili per il servizio IotHub |
> | Azione | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Ottiene la metrica disponibile per il servizio IotHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Ottiene la metrica di quota |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/Read | Ottiene la risorsa tenant IotHub |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Effettua il test di un messaggio con tutte le route esistenti |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Effettua il test di un messaggio con la route di test fornita |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Ottiene lo stato di tutti gli endpoint di routing per un servizio IotHub |
> | Azione | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Ottenere il risultato dell'operazione di inserimento asincrono per l'hub tenant SecuritySettings |
> | Azione | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | Ottenere le impostazioni del Centro sicurezza di Azure nell'hub tenant di Internet delle cose |
> | Azione | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | Aggiornare le impostazioni del Centro sicurezza di Azure nell'hub tenant di Internet |
> | Azione | Microsoft.Devices/elasticPools/iotHubTenants/Write | Crea o aggiorna la risorsa tenant IotHub |
> | Azione | Microsoft.Devices/ElasticPools/metricDefinitions/read | Ottiene la metrica disponibile per il servizio IotHub |
> | Azione | Microsoft.Devices/iotHubs/certificates/Delete | Elimina il certificato |
> | Azione | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Genera il codice di verifica |
> | Azione | Microsoft.Devices/iotHubs/certificates/Read | Recupera il certificato |
> | Azione | Microsoft.Devices/iotHubs/certificates/verify/Action | Verifica la risorsa certificato |
> | Azione | Microsoft.Devices/iotHubs/certificates/Write | Crea o aggiorna il certificato |
> | Azione | Microsoft.Devices/iotHubs/Delete | Elimina una risorsa IotHub |
> | Azione | Microsoft.Devices/IotHubs/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/IotHubs/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Elimina il filtro di Griglia di eventi |
> | Azione | Microsoft.Devices/iotHubs/eventGridFilters/Read | Ottiene il filtro di Griglia di eventi |
> | Azione | Microsoft.Devices/iotHubs/eventGridFilters/Write | Crea un nuovo filtro di Griglia di eventi o ne aggiorna uno esistente |
> | Azione | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Elimina un gruppo di consumer EventHub |
> | Azione | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Ottiene il gruppo o i gruppi di consumer EventHub |
> | Azione | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Crea un gruppo di consumer EventHub |
> | Azione | Microsoft.Devices/iotHubs/exportDevices/Action | Esporta dispositivi |
> | Azione | Microsoft.Devices/iotHubs/importDevices/Action | Importa dispositivi |
> | Azione | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Ottiene la chiave IotHub per il nome specificato |
> | Azione | Microsoft.Devices/iotHubs/iotHubStats/Read | Ottiene le statistiche IotHub |
> | Azione | Microsoft.Devices/iotHubs/jobs/Read | Ottiene i dettagli del processo o dei processi inviati per un servizio IotHub specifico |
> | Azione | Microsoft.Devices/iotHubs/listkeys/Action | Ottiene tutte le chiavi IotHub |
> | Azione | Microsoft.Devices/IotHubs/logDefinitions/read | Ottiene le definizioni di log disponibili per il servizio IotHub |
> | Azione | Microsoft.Devices/IotHubs/metricDefinitions/read | Ottiene la metrica disponibile per il servizio IotHub |
> | Azione | Microsoft.Devices/iotHubs/operationresults/Read | Ottiene il risultato di un'operazione (API obsoleta) |
> | Azione | Microsoft.Devices/iotHubs/quotaMetrics/Read | Ottiene la metrica di quota |
> | Azione | Microsoft.Devices/iotHubs/Read | Ottiene la risorsa o le risorse IotHub |
> | Azione | Microsoft.Devices/iotHubs/routing/$testall/Action | Effettua il test di un messaggio con tutte le route esistenti |
> | Azione | Microsoft.Devices/iotHubs/routing/$testnew/Action | Effettua il test di un messaggio con la route di test fornita |
> | Azione | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Ottiene lo stato di tutti gli endpoint di routing per un servizio IotHub |
> | Azione | Microsoft. Devices/iotHubs/securitySettings/operationResults/Read | Ottenere il risultato dell'operazione di inserimento asincrono per l'hub SecuritySettings |
> | Azione | Microsoft. Devices/iotHubs/securitySettings/Read | Ottenere le impostazioni del Centro sicurezza di Azure nell'hub Internet |
> | Azione | Microsoft. Devices/iotHubs/securitySettings/Write | Aggiornare le impostazioni del Centro sicurezza di Azure nell'hub Internet |
> | Azione | Microsoft.Devices/iotHubs/skus/Read | Ottiene gli SKU IotHub validi |
> | Azione | Microsoft.Devices/iotHubs/Write | Crea o aggiorna una risorsa IotHub |
> | Azione | Microsoft.Devices/locations/operationresults/Read | Ottiene il risultato dell'operazione Location Based |
> | Azione | Microsoft.Devices/operationresults/Read | Ottiene il risultato di un'operazione |
> | Azione | Microsoft.Devices/operations/Read | Ottiene tutte le operazioni ResourceProvider |
> | Azione | Microsoft.Devices/provisioningServices/certificates/Delete | Elimina il certificato |
> | Azione | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Genera il codice di verifica |
> | Azione | Microsoft.Devices/provisioningServices/certificates/Read | Recupera il certificato |
> | Azione | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verifica la risorsa certificato |
> | Azione | Microsoft.Devices/provisioningServices/certificates/Write | Crea o aggiorna il certificato |
> | Azione | Microsoft.Devices/provisioningServices/Delete | Elimina la risorsa IotDps |
> | Azione | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Ottiene le chiavi IotDps per il nome della chiave |
> | Azione | Microsoft.Devices/provisioningServices/listkeys/Action | Ottiene tutte le chiavi IotDps |
> | Azione | Microsoft.Devices/provisioningServices/logDefinitions/read | Ottiene le definizioni di log disponibili per il servizio di provisioning |
> | Azione | Microsoft.Devices/provisioningServices/metricDefinitions/read | Ottiene la metrica disponibile per il servizio di provisioning |
> | Azione | Microsoft.Devices/provisioningServices/operationresults/Read | Ottiene il risultato di un'operazione DPS |
> | Azione | Microsoft.Devices/provisioningServices/Read | Ottiene la risorsa IotDps |
> | Azione | Microsoft.Devices/provisioningServices/skus/Read | Ottiene SKU IotDps validi |
> | Azione | Microsoft.Devices/provisioningServices/Write | Crea la risorsa IotDps |
> | Azione | Microsoft.Devices/register/action | Registra la sottoscrizione per il provider di risorse del servizio IotHub e consente la creazione di risorse IotHub |
> | Azione | Microsoft.Devices/usages/Read | Ottiene dettagli sull'utilizzo della sottoscrizione per questo provider. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DevSpaces/controllers/delete | Consente di eliminare i servizi del controller di Azure Dev Spaces e dataplane |
> | Azione | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Elenca i dettagli di connessione per l'infrastruttura del controller di Azure Dev Spaces |
> | Azione | Microsoft.DevSpaces/controllers/read | Legge le proprietà del controller di Azure Dev Spaces |
> | Azione | Microsoft.DevSpaces/controllers/write | Crea o aggiorna le proprietà del controller di Azure Dev Spaces |
> | Azione | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Verifica mapping controller esistente per un host contenitore |
> | Azione | Microsoft.DevSpaces/locations/operationresults/read | Lettura dello stato di un'operazione asincrona |
> | Azione | Microsoft.DevSpaces/register/action | Registra il provider di risorse Microsoft Dev Spaces con una sottoscrizione |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DevTestLab/labCenters/delete | Elimina i centri del lab. |
> | Azione | Microsoft.DevTestLab/labCenters/read | Legge i centri del lab. |
> | Azione | Microsoft.DevTestLab/labCenters/write | Aggiunge o modifica i centri del lab. |
> | Azione | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Esegue la lettura di modelli di Azure Resource Manager. |
> | Azione | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Genera un modello di Azure Resource Manager per l'elemento specificato, carica i file necessari in un account di archiviazione e convalida l'artefatto generato. |
> | Azione | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Esegue la lettura di elementi. |
> | Azione | Microsoft.DevTestLab/labs/artifactSources/delete | Elimina origini elemento. |
> | Azione | Microsoft.DevTestLab/labs/artifactSources/read | Esegue la lettura di origini elemento. |
> | Azione | Microsoft.DevTestLab/labs/artifactSources/write | Aggiunge o modifica origini elemento. |
> | Azione | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Attesta una macchina virtuale attestabile casualmente nel lab. |
> | Azione | Microsoft.DevTestLab/labs/costs/read | Esegue la lettura di costi. |
> | Azione | Microsoft.DevTestLab/labs/costs/write | Aggiunge o modifica costi. |
> | Azione | Microsoft.DevTestLab/labs/CreateEnvironment/action | Crea macchine virtuali in un lab. |
> | Azione | Microsoft.DevTestLab/labs/customImages/delete | Elimina immagini personalizzate. |
> | Azione | Microsoft.DevTestLab/labs/customImages/read | Esegue la lettura di immagini personalizzate. |
> | Azione | Microsoft.DevTestLab/labs/customImages/write | Aggiunge o modifica immagini personalizzate. |
> | Azione | Microsoft.DevTestLab/labs/delete | Elimina lab. |
> | Azione | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Verificare che l'utente corrente disponga di un profilo valido nel Lab. |
> | Azione | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Esporta l'utilizzo delle risorse del lab in un account di archiviazione |
> | Azione | Microsoft.DevTestLab/labs/formulas/delete | Elimina le formule. |
> | Azione | Microsoft.DevTestLab/labs/formulas/read | Esegue la lettura di formule. |
> | Azione | Microsoft.DevTestLab/labs/formulas/write | Aggiunge o modifica le formule. |
> | Azione | Microsoft.DevTestLab/labs/galleryImages/read | Esegue la lettura delle immagini della raccolta. |
> | Azione | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Genera un URI per il caricamento di immagini disco personalizzate in un lab. |
> | Azione | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importa una macchina virtuale in un lab diverso. |
> | Azione | Microsoft.DevTestLab/labs/ListVhds/action | Elenca le immagini di un disco disponibili per la creazione di immagini personalizzate. |
> | Azione | Microsoft.DevTestLab/labs/notificationChannels/delete | Elimina canali di notifica. |
> | Azione | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Invia la notifica al canale specificato. |
> | Azione | Microsoft.DevTestLab/labs/notificationChannels/read | Leggere canali di notifica. |
> | Azione | Microsoft.DevTestLab/labs/notificationChannels/write | Aggiunta o modifica di canali di notifica. |
> | Azione | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Valuta i criteri del lab. |
> | Azione | Microsoft.DevTestLab/labs/policySets/policies/delete | Elimina i criteri. |
> | Azione | Microsoft.DevTestLab/labs/policySets/policies/read | Esegue la lettura di criteri. |
> | Azione | Microsoft.DevTestLab/labs/policySets/policies/write | Aggiunge o modifica i criteri. |
> | Azione | Microsoft.DevTestLab/labs/policySets/read | Legge i set di criteri. |
> | Azione | Microsoft.DevTestLab/labs/read | Esegue la lettura di lab. |
> | Azione | Microsoft.DevTestLab/labs/schedules/delete | Elimina le pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/schedules/Execute/action | Esegue una pianificazione. |
> | Azione | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Elenca tutte le pianificazioni applicabili |
> | Azione | Microsoft.DevTestLab/labs/schedules/read | Esegue la lettura delle pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/schedules/write | Aggiunge o modifica pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/serviceRunners/delete | Elimina strumenti di esecuzione servizio. |
> | Azione | Microsoft.DevTestLab/labs/serviceRunners/read | Esegue la lettura di strumenti di esecuzione servizio. |
> | Azione | Microsoft.DevTestLab/labs/serviceRunners/write | Aggiunge o modifica strumenti di esecuzione servizio. |
> | Azione | Microsoft.DevTestLab/labs/sharedGalleries/delete | Elimina le raccolte condivise. |
> | Azione | Microsoft.DevTestLab/labs/sharedGalleries/read | Leggere le raccolte condivise. |
> | Azione | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Elimina immagini condivise. |
> | Azione | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Leggere immagini condivise. |
> | Azione | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Aggiungere o modificare immagini condivise. |
> | Azione | Microsoft.DevTestLab/labs/sharedGalleries/write | Aggiungere o modificare le raccolte condivise. |
> | Azione | Microsoft.DevTestLab/labs/users/delete | Elimina profili utente. |
> | Azione | Microsoft.DevTestLab/labs/users/disks/Attach/action | Collegare e creare il lease del disco alla macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/users/disks/delete | Elimina dischi. |
> | Azione | Microsoft.DevTestLab/labs/users/disks/Detach/action | Rimuove il disco collegato alla macchina virtuale e interrompe il lease. |
> | Azione | Microsoft.DevTestLab/labs/users/disks/read | Esegue la lettura di dischi. |
> | Azione | Microsoft.DevTestLab/labs/users/disks/write | Aggiunge o modifica dischi. |
> | Azione | Microsoft.DevTestLab/labs/users/environments/delete | Elimina ambienti. |
> | Azione | Microsoft.DevTestLab/labs/users/environments/read | Esegue la lettura di ambienti. |
> | Azione | Microsoft.DevTestLab/labs/users/environments/write | Aggiunge o modifica ambienti. |
> | Azione | Microsoft.DevTestLab/labs/users/read | Esegue la lettura di profili utente. |
> | Azione | Microsoft.DevTestLab/labs/users/secrets/delete | Elimina segreti. |
> | Azione | Microsoft.DevTestLab/labs/users/secrets/read | Esegue la lettura di segreti. |
> | Azione | Microsoft.DevTestLab/labs/users/secrets/write | Aggiunge o modifica segreti. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Elimina le infrastrutture di servizi. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Elenca le pianificazioni di avvio/arresto applicabili, se presenti. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Legge le infrastrutture di servizi. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Elimina le pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Esegue una pianificazione. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Esegue la lettura delle pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Aggiunge o modifica pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Avvia un'infrastruttura di servizi. |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Arresta un'infrastruttura di servizi |
> | Azione | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Aggiunge o modifica le infrastrutture di servizi. |
> | Azione | Microsoft.DevTestLab/labs/users/write | Aggiunge o modifica profili utente. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Collega un disco dati nuovo o esistente alla macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Applica elementi a una macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Consente di assumere la proprietà di una macchina virtuale esistente |
> | Azione | Microsoft. DevTestLab/Labs/virtualMachines/ClearArtifactResults/Action | Cancella i risultati dell'artefatto della macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/delete | Elimina macchine virtuali. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Rimuove il disco specificato dalla macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Ottiene una stringa che rappresenta il contenuto del file RDP per la macchina virtuale |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Elenca le pianificazioni di avvio/arresto applicabili, se presenti. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/read | Esegue la lettura di macchine virtuali. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Ridistribuisce una macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ridimensiona la macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Riavvia una macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Elimina le pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Esegue una pianificazione. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Esegue la lettura delle pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Aggiunge o modifica pianificazioni. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Avvia una macchina virtuale. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Arrestare una macchina virtuale |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Trasferisce tutti i dischi dati collegati alla macchina virtuale in modo che diventino di proprietà dell'utente corrente. |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Rilascia la proprietà di una macchina virtuale esistente |
> | Azione | Microsoft.DevTestLab/labs/virtualMachines/write | Aggiunge o modifica macchine virtuali. |
> | Azione | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Delete | Elimina bastionhosts. |
> | Azione | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Read | Leggere bastionhosts. |
> | Azione | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Write | Aggiungere o modificare bastionhosts. |
> | Azione | Microsoft.DevTestLab/labs/virtualNetworks/delete | Elimina reti virtuali. |
> | Azione | Microsoft.DevTestLab/labs/virtualNetworks/read | Esegue la lettura di reti virtuali. |
> | Azione | Microsoft.DevTestLab/labs/virtualNetworks/write | Aggiunge o modifica reti virtuali. |
> | Azione | Microsoft.DevTestLab/labs/vmPools/delete | Elimina i pool di macchine virtuali. |
> | Azione | Microsoft.DevTestLab/labs/vmPools/read | Legge i pool di macchine virtuali. |
> | Azione | Microsoft.DevTestLab/labs/vmPools/write | Aggiunge o modifica pool di macchine virtuali. |
> | Azione | Microsoft.DevTestLab/labs/write | Aggiunge o modifica lab. |
> | Azione | Microsoft.DevTestLab/locations/operations/read | Esegue la lettura delle operazioni. |
> | Azione | Microsoft.DevTestLab/register/action | Registra la sottoscrizione |
> | Azione | Microsoft.DevTestLab/schedules/delete | Elimina le pianificazioni. |
> | Azione | Microsoft.DevTestLab/schedules/Execute/action | Esegue una pianificazione. |
> | Azione | Microsoft.DevTestLab/schedules/read | Esegue la lettura delle pianificazioni. |
> | Azione | Microsoft.DevTestLab/schedules/Retarget/action | Aggiorna l'ID risorsa di destinazione di una pianificazione. |
> | Azione | Microsoft.DevTestLab/schedules/write | Aggiunge o modifica pianificazioni. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DocumentDB/databaseAccountNames/read | Controlla la disponibilità del nome. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Eliminare una raccolta. Applicabile solo ai tipi di API: "MongoDB". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API: "MongoDB". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Legge una raccolta o elenca tutte le raccolte. Applicabile solo ai tipi di API: "MongoDB". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API: "MongoDB". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Leggi una velocità effettiva della raccolta. Applicabile solo ai tipi di API: "MongoDB". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Aggiornare una velocità effettiva della raccolta. Applicabile solo ai tipi di API: "MongoDB". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Creazione o aggiornamento di una raccolta. Applicabile solo ai tipi di API: "MongoDB". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Eliminare un contenitore. Applicabile solo ai tipi di API:' SQL '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' SQL '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Leggere un contenitore o elencare tutti i contenitori. Applicabile solo ai tipi di API:' SQL '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' SQL '. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Lettura della velocità effettiva di un contenitore. Applicabile solo ai tipi di API:' SQL '. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Aggiornare la velocità effettiva di un contenitore. Applicabile solo ai tipi di API:' SQL '. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Creazione o aggiornamento di un contenitore. Applicabile solo ai tipi di API:' SQL '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Eliminare un database. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Eliminare un grafico. Applicabile solo ai tipi di API:' gremlin'. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' gremlin'. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Leggere un grafico o elencare tutti i grafici. Applicabile solo ai tipi di API:' gremlin'. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' gremlin'. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Leggi una velocità effettiva del grafo. Applicabile solo ai tipi di API:' gremlin'. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Aggiornare una velocità effettiva del grafico. Applicabile solo ai tipi di API:' gremlin'. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Crea o aggiorna un grafico. Applicabile solo ai tipi di API:' gremlin'. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Leggere un database o elencare tutti i database. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Leggi una velocità effettiva del database. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Aggiornare una velocità effettiva del database. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Creare un database. Applicabile solo ai tipi di API:' SQL ',' MongoDB ',' gremlin''. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Eliminare uno spazio. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Lettura di uno spazio di tasto o elenco di tutti gli spazi. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API: "Cassandra". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Leggi una velocità effettiva di spazio. Applicabile solo ai tipi di API: "Cassandra". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Aggiornare una velocità effettiva di spazio. Applicabile solo ai tipi di API: "Cassandra". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Eliminare una tabella. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Leggere una tabella o un elenco di tutte le tabelle. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API: "Cassandra". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Leggi una velocità effettiva della tabella. Applicabile solo ai tipi di API: "Cassandra". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Aggiornare una velocità effettiva della tabella. Applicabile solo ai tipi di API: "Cassandra". Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Creare o aggiornare una tabella. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Creare uno spazio. Applicabile solo ai tipi di API: "Cassandra". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Eliminare una tabella. Applicabile solo ai tipi di API:' Table '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' Table '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Leggere una tabella o un elenco di tutte le tabelle. Applicabile solo ai tipi di API:' Table '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Lettura dello stato dell'operazione asincrona. Applicabile solo ai tipi di API:' Table '. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Leggi una velocità effettiva della tabella. Applicabile solo ai tipi di API:' Table '. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Aggiornare una velocità effettiva della tabella. Applicabile solo ai tipi di API:' Table '. Applicabile solo per i tipi di impostazione: "velocità effettiva". |
> | Azione | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Creare o aggiornare una tabella. Applicabile solo ai tipi di API:' Table '. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/backup/action | Invia una richiesta per configurare il backup |
> | Azione | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Modifica il gruppo di risorse di un account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Esegue la lettura delle definizioni delle metriche delle raccolte. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Esegue la lettura della metrica delle raccolte. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Legge la metrica a livello di chiave della partizione dell'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Legge la metrica a livello della partizione dell'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Legge le partizioni degli account del database in una raccolta |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Legge gli utilizzi a livello della partizione dell'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Esegue la lettura degli utilizzi delle raccolte. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Esegue la lettura delle definizioni delle metriche dei database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Esegue la lettura della metrica dei database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Esegue la lettura degli utilizzi dei database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/delete | Elimina gli account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Modifica le priorità di failover di aree di un account di database. Usata per eseguire l'operazione di failover manuale |
> | Azione | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Ottenere i criteri di backup per l'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Ottiene le stringhe di connessione per un account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Elenca le chiavi di un account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Esegue la lettura delle definizioni delle metriche degli account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/metrics/read | Esegue la lettura della metrica degli account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Porta offline un'area di un account di database.  |
> | Azione | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Porta online un'area di un account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Legge lo stato dell'operazione asincrona |
> | Azione | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metrica di latenza di lettura |
> | Azione | Microsoft.DocumentDB/databaseAccounts/percentile/read | Legge i percentili delle latenze di replica |
> | Azione | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Legge la metrica di latenza per una specifica area di origine e di destinazione |
> | Azione | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Legge la metrica di latenza per una specifica area di destinazione |
> | Azione | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Eliminare un proxy di connessione all'endpoint privato dell'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Lettura dello stato dell'operazione asincrona del proxy di connessione all'endpoint privato |
> | Azione | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Leggere un proxy di connessione all'endpoint privato dell'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Convalida di un proxy di connessione all'endpoint privato dell'account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Crea o aggiorna un proxy di connessione all'endpoint privato dell'account di database |
> | Azione | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Delete | Eliminare una connessione all'endpoint privato di un account di database |
> | Azione | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/Read | Lettura dello stato dell'operazione asincrona privateEndpointConnenctions |
> | Azione | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Read | Lettura di una connessione a un endpoint privato o elenco di tutte le connessioni a endpoint privato di un account di database |
> | Azione | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Write | Crea o aggiorna una connessione all'endpoint privato di un account di database |
> | Azione | Microsoft. DocumentDB/databaseAccounts/privateLinkResources/Read | Leggere una risorsa di collegamento privato o elencare tutte le risorse di collegamento privato di un account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/read | Esegue la lettura di un account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Legge le chiavi di sola lettura degli account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Legge le chiavi di sola lettura degli account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Ruota le chiavi di un account di database |
> | Azione | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Legge la metrica delle raccolte dell'area. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Legge la metrica a livello di chiave della partizione dell'account di database dell'area |
> | Azione | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Legge la metrica a livello della partizione dell'account di database dell'area |
> | Azione | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Legge le partizioni degli account di database dell'area in una raccolta |
> | Azione | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Legge la metrica degli account per il database e l'area. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/restore/action | Invia una richiesta di ripristino |
> | Azione | Microsoft.DocumentDB/databaseAccounts/usages/read | Esegue la lettura degli utilizzi degli account di database. |
> | Azione | Microsoft.DocumentDB/databaseAccounts/write | Aggiorna un account di database. |
> | Azione | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Avvisa Microsoft.DocumentDB che la rete virtuale o la subnet è in fase di eliminazione |
> | Azione | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Legge lo stato dell'operazione asincrona deleteVirtualNetworkOrSubnets |
> | Azione | Microsoft.DocumentDB/locations/operationsStatus/read | Legge lo stato delle operazioni asincrone |
> | Azione | Microsoft.DocumentDB/operationResults/read | Legge lo stato dell'operazione asincrona |
> | Azione | Microsoft.DocumentDB/operations/read | Legge le operazioni disponibili per Microsoft DocumentDB  |
> | Azione | Microsoft.DocumentDB/register/action |  Registra il provider di risorse Microsoft DocumentDB per la sottoscrizione |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.DomainRegistration/checkDomainAvailability/Action | Controlla se un dominio è disponibile per l'acquisto |
> | Azione | Microsoft.DomainRegistration/domains/Delete | Elimina un dominio esistente. |
> | Azione | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Elimina l'identificatore di proprietà |
> | Azione | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Elenca gli identificatori di proprietà |
> | Azione | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Ottiene l'identificatore di proprietà |
> | Azione | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Crea o aggiorna l'identificatore |
> | Azione | Microsoft.DomainRegistration/domains/operationresults/Read | Ottiene un'operazione di dominio |
> | Azione | Microsoft.DomainRegistration/domains/Read | Ottiene l'elenco dei domini |
> | Azione | Microsoft.DomainRegistration/domains/Read | Ottiene il dominio |
> | Azione | Microsoft.DomainRegistration/domains/renew/Action | Rinnova un dominio esistente. |
> | Azione | Microsoft.DomainRegistration/domains/Write | Aggiunge un nuovo dominio o ne aggiorna uno esistente |
> | Azione | Microsoft.DomainRegistration/generateSsoRequest/Action | Genera una richiesta per l'accesso al centro di controllo del dominio. |
> | Azione | Microsoft.DomainRegistration/listDomainRecommendations/Action | Recupera l'elenco di consigli sui domini in base alle parole chiave |
> | Azione | Microsoft.DomainRegistration/operations/Read | Elenca tutte le operazioni dalla registrazione del dominio del servizio app |
> | Azione | Microsoft.DomainRegistration/register/action | Registra il provider di risorse domini Microsoft per la sottoscrizione |
> | Azione | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Azione elenco contratti |
> | Azione | Microsoft.DomainRegistration/topLevelDomains/Read | Ottiene i domini di massimo livello |
> | Azione | Microsoft.DomainRegistration/topLevelDomains/Read | Ottiene il dominio di massimo livello |
> | Azione | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Convalida l'oggetto acquisto di dominio senza inviarlo |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.EventGrid/domains/delete | Elimina un dominio |
> | Azione | Microsoft.EventGrid/domains/listKeys/action | Elenca le chiavi per un dominio |
> | Azione | Microsoft. EventGrid/Domains/Providers/Microsoft. Insights/logDefinitions/Read | Consente l'accesso ai log di diagnostica |
> | Azione | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Recupera le metriche disponibili per i domini |
> | Azione | Microsoft.EventGrid/domains/read | Legge un dominio |
> | Azione | Microsoft.EventGrid/domains/regenerateKey/action | Rigenera la chiave per un dominio |
> | Azione | Microsoft. EventGrid/Domains/topics/Delete | Eliminare un argomento di dominio |
> | Azione | Microsoft.EventGrid/domains/topics/read | Legge un argomento di dominio |
> | Azione | Microsoft. EventGrid/domini/argomenti/scrittura | Creare o aggiornare un argomento di dominio |
> | Azione | Microsoft.EventGrid/domains/write | Crea o aggiorna un dominio |
> | Azione | Microsoft.EventGrid/eventSubscriptions/delete | Eliminare un sottoscrizione evento |
> | Azione | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Ottiene l'URL completo per la sottoscrizione evento |
> | Azione | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per le sottoscrizioni evento |
> | Azione | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per le sottoscrizioni evento |
> | Azione | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per le sottoscrizioni evento |
> | Azione | Microsoft.EventGrid/eventSubscriptions/read | Leggi un sottoscrizione evento |
> | Azione | Microsoft.EventGrid/eventSubscriptions/write | Crea o aggiorna un sottoscrizione evento |
> | Azione | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per gli argomenti |
> | Azione | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per gli argomenti |
> | Azione | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per gli argomenti |
> | Azione | Microsoft.EventGrid/extensionTopics/read | Legge un extensionTopic. |
> | Azione | Microsoft.EventGrid/locations/eventSubscriptions/read | Elencare sottoscrizioni di eventi per area |
> | Azione | Microsoft.EventGrid/locations/operationResults/read | Legge il risultato di un'operazione a livello di area |
> | Azione | Microsoft.EventGrid/locations/operationsStatus/read | Legge lo stato di un'operazione a livello di area |
> | Azione | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Elencare sottoscrizioni di eventi per area per tipo di argomento |
> | Azione | Microsoft.EventGrid/operationResults/read | Legge il risultato di un'operazione |
> | Azione | Microsoft.EventGrid/operations/read | Elenca le operazioni EventGrid. |
> | Azione | Microsoft.EventGrid/operationsStatus/read | Legge lo stato di un'operazione |
> | Azione | Microsoft.EventGrid/register/action | Registra la sottoscrizione per il provider di risorse EventGrid. |
> | Azione | Microsoft.EventGrid/topics/delete | Eliminare un argomento |
> | Azione | Microsoft.EventGrid/topics/listKeys/action | Elenca le chiavi per un argomento |
> | Azione | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per gli argomenti |
> | Azione | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per gli argomenti |
> | Azione | Microsoft. EventGrid/topics/Providers/Microsoft. Insights/logDefinitions/Read | Consente l'accesso ai log di diagnostica |
> | Azione | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per gli argomenti |
> | Azione | Microsoft.EventGrid/topics/read | Legge un argomento |
> | Azione | Microsoft.EventGrid/topics/regenerateKey/action | Rigenera la chiave per un argomento |
> | Azione | Microsoft.EventGrid/topics/write | Crea o aggiorna un argomento |
> | Azione | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Elencare le sottoscrizioni di eventi globali per tipo di argomento |
> | Azione | Microsoft.EventGrid/topictypes/eventtypes/read | Legge i tipi di evento supportati da un tipo di argomento |
> | Azione | Microsoft.EventGrid/topictypes/read | Legge un tipo di argomento |
> | Azione | Microsoft.EventGrid/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.EventHub/availableClusterRegions/read | Operazione di lettura per elencare i cluster di pre-provisioning disponibili in base all'area di Azure. |
> | Azione | Microsoft.EventHub/checkNameAvailability/action | Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. |
> | Azione | Microsoft.EventHub/checkNamespaceAvailability/action | Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. Questa API è deprecata, usare invece CheckNameAvailability. |
> | Azione | Microsoft.EventHub/clusters/delete | Elimina una risorsa cluster esistente. |
> | Azione | Microsoft.EventHub/clusters/namespaces/read | Elenca gli ID Azure Resource Manager per gli spazi dei nomi all'interno di un cluster. |
> | Azione | Microsoft.EventHub/clusters/operationresults/read | Ottenere lo stato di un'operazione del cluster asincrona. |
> | Azione | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Recupera l'elenco delle descrizioni delle risorse di metrica del cluster. |
> | Azione | Microsoft.EventHub/clusters/read | Recupera la descrizione della risorsa cluster. |
> | Azione | Microsoft.EventHub/clusters/write | Crea o modifica una risorsa cluster esistente. |
> | Azione | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Elimina le regole della rete virtuale nel provider di risorse dell'hub eventi per la rete virtuale specificata |
> | Azione | Microsoft.EventHub/namespaces/authorizationRules/action | Aggiorna la regola di autorizzazione dello spazio dei nomi. Questa API è deprecata. Usare invece una chiamata PUT per aggiornare la regola di autorizzazione dello spazio dei nomi... Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.EventHub/namespaces/authorizationRules/delete | Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita.  |
> | Azione | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Ottiene la stringa di connessione per lo spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/authorizationRules/read | Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi. |
> | Azione | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.EventHub/namespaces/authorizationRules/write | Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.EventHub/namespaces/Delete | Elimina la risorsa spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Ottiene le chiavi delle regole di autorizzazione per lo spazio dei nomi primario di ripristino di emergenza |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Ottiene le regole di autorizzazione dello spazio dei nomi primario di ripristino di emergenza |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Disabilita il ripristino di emergenza e arresta la replica delle modifiche dagli spazi dei nomi primari a quelli secondari. |
> | Azione | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica la disponibilità di alias dello spazio dei nomi nella sottoscrizione specificata. |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Elimina la configurazione di ripristino di emergenza associata allo spazio dei nomi. Questa operazione può essere chiamata solo tramite lo spazio dei nomi primario. |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Chiama il failover di ripristino di emergenza con ridondanza geografica e riconfigura l'alias dello spazio dei nomi affinché punti allo spazio dei nomi secondario. |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Recupera la configurazione di ripristino di emergenza associata allo spazio dei nomi. |
> | Azione | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Crea o aggiorna la configurazione di ripristino di emergenza associata allo spazio dei nomi. |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operazione per aggiornare EventHub. Questa operazione non è supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione. |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operazione per l'eliminazione di regole di autorizzazione dell'hub eventi |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Ottiene la stringa di connessione per l'hub eventi |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Ottiene l'elenco delle regole di autorizzazione dell'hub eventi |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Crea regole di autorizzazione dell'hub eventi e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operazione per l'eliminazione di risorse gruppo consumer |
> | Azione | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Ottiene l'elenco delle descrizioni delle risorse gruppo consumer |
> | Azione | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Crea o aggiorna le proprietà del gruppo consumer. |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/Delete | Operazione per l'eliminazione di risorse hub eventi |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/read | Ottiene l'elenco delle descrizioni delle risorse dell'hub eventi |
> | Azione | Microsoft.EventHub/namespaces/eventhubs/write | Crea o aggiorna le proprietà dell'hub eventi. |
> | Azione | Microsoft.EventHub/namespaces/ipFilterRules/delete | Elimina la risorsa filtro IP |
> | Azione | Microsoft.EventHub/namespaces/ipFilterRules/read | Recupera la risorsa filtro IP |
> | Azione | Microsoft.EventHub/namespaces/ipFilterRules/write | Crea la risorsa filtro IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Ricevere messaggi |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Inviare messaggi |
> | Azione | Microsoft.EventHub/namespaces/messagingPlan/read | Ottiene il piano di messaggistica per uno spazio dei nomi.<br>Questa API è deprecata.<br>Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive.<br>Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.EventHub/namespaces/messagingPlan/write | Aggiorna il piano di messaggistica per uno spazio dei nomi.<br>Questa API è deprecata.<br>Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive.<br>Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft. EventHub/Namespaces/networkruleset/Delete | Elimina la risorsa regola di rete virtuale |
> | Azione | Microsoft. EventHub/Namespaces/networkruleset/Read | Ottiene la risorsa NetworkRuleSet |
> | Azione | Microsoft. EventHub/Namespaces/networkruleset/Write | Crea risorsa regola di rete virtuale |
> | Azione | Microsoft.EventHub/namespaces/networkrulesets/delete | Elimina la risorsa regola di rete virtuale |
> | Azione | Microsoft.EventHub/namespaces/networkrulesets/read | Ottiene la risorsa NetworkRuleSet |
> | Azione | Microsoft.EventHub/namespaces/networkrulesets/write | Crea risorsa regola di rete virtuale |
> | Azione | Microsoft.EventHub/namespaces/operationresults/read | Operazione di recupero dello stato dello spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/read | Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi |
> | Azione | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Rimuove uno spazio dei nomi ACS |
> | Azione | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Elimina la risorsa regola di rete virtuale |
> | Azione | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Recupera la risorsa regola di rete virtuale |
> | Azione | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Crea risorsa regola di rete virtuale |
> | Azione | Microsoft.EventHub/namespaces/write | Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi. |
> | Azione | Microsoft.EventHub/operations/read | Ottiene operazioni |
> | Azione | Microsoft.EventHub/register/action | Registra la sottoscrizione per il provider di risorse hub eventi e abilita la creazione di risorse hub eventi |
> | Azione | Microsoft.EventHub/sku/read | Ottiene l'elenco delle descrizioni di una risorsa SKU |
> | Azione | Microsoft.EventHub/sku/regions/read | Ottiene l'elenco delle descrizioni di una risorsa SkuRegions |
> | Azione | Microsoft.EventHub/unregister/action | Registra il provider di risorse hub eventi |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Features/features/read | Ottiene le funzionalità di una sottoscrizione. |
> | Azione | Microsoft.Features/operations/read | Recupera l'elenco delle operazioni. |
> | Azione | Microsoft.Features/providers/features/read | Ottiene la funzionalità di una sottoscrizione in un provider di risorse specificato. |
> | Azione | Microsoft.Features/providers/features/register/action | Registra la funzionalità per una sottoscrizione in un provider di risorse specificato. |
> | Azione | Microsoft.Features/providers/features/unregister/action | Annulla la registrazione della funzionalità per una sottoscrizione in un provider di risorse specificato. |
> | Azione | Microsoft.Features/register/action | Registra la funzionalità di una sottoscrizione. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Elimina l'assegnazione della configurazione Guest. |
> | Azione | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Assegnazione di configurazione guest Get. |
> | Azione | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Recupera il rapporto di assegnazione della configurazione guest. |
> | Azione | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Creare una nuova assegnazione di configurazione guest Get. |
> | Azione | Microsoft. GuestConfiguration/Operations/Read | Ottiene le operazioni per il provider di risorse Microsoft. GuestConfiguration |
> | Azione | Microsoft.GuestConfiguration/register/action | Registra la sottoscrizione per il provider di risorse Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.HDInsight/clusters/applications/delete | Elimina l'applicazione per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/applications/read | Ottiene l'applicazione per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/applications/write | Crea o aggiorna l'applicazione per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/changerdpsetting/action | Modifica l'impostazione RDP per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/configurations/action | Aggiorna la configurazione del cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/configurations/action | Ottiene le configurazioni del cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/configurations/read | Ottiene le configurazioni del cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/delete | Elimina un cluster HDInsight |
> | Azione | Microsoft. HDInsight/Clusters/Extensions/Delete | Eliminare l'estensione del cluster per il cluster HDInsight |
> | Azione | Microsoft. HDInsight/Clusters/Extensions/Read | Ottenere l'estensione del cluster per il cluster HDInsight |
> | Azione | Microsoft. HDInsight/Clusters/Extensions/Write | Creare un'estensione del cluster per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/getGatewaySettings/action | Ottenere le impostazioni del gateway per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per il cluster HDInsight della risorsa |
> | Azione | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per il cluster HDInsight della risorsa |
> | Azione | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/read | Ottiene informazioni sul cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/roles/resize/action | Ridimensiona un cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aggiornare le impostazioni del gateway per il cluster HDInsight |
> | Azione | Microsoft.HDInsight/clusters/write | Crea o aggiorna un cluster HDInsight |
> | Azione | Microsoft.HDInsight/locations/capabilities/read | Ottiene le funzionalità di sottoscrizione |
> | Azione | Microsoft.HDInsight/locations/checkNameAvailability/read | Controlla la disponibilità del nome |
> | Azione | Microsoft. HDInsight/Register/Action | Registrare il provider di risorse HDInsight per la sottoscrizione |
> | Azione | Microsoft. HDInsight/Annulla registrazione/azione | Annulla la registrazione del provider di risorse HDInsight per la sottoscrizione |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ImportExport/jobs/delete | Elimina il processo esistente. |
> | Azione | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Ottiene le chiavi BitLocker per il processo specificato. |
> | Azione | Microsoft.ImportExport/jobs/read | Ottiene le proprietà per il processo specificato o restituisce l'elenco dei processi. |
> | Azione | Microsoft.ImportExport/jobs/write | Crea un processo con i parametri specificati o aggiorna le proprietà o i tag per il processo specificato. |
> | Azione | Microsoft.ImportExport/locations/read | Ottiene le proprietà per il percorso specificato o restituisce l'elenco dei percorsi. |
> | Azione | Microsoft. ImportExport/Operations/Read | Ottiene le operazioni supportate dal provider di risorse. |
> | Azione | Microsoft.ImportExport/register/action | Registra la sottoscrizione per il provider di risorse importazione/esportazione e consente la creazione di processi di importazione/esportazione. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Insights/ActionGroups/Delete | Elimina un gruppo di azioni |
> | Azione | Microsoft.Insights/ActionGroups/Read | Legge un gruppo di azioni |
> | Azione | Microsoft.Insights/ActionGroups/Write | Crea o aggiorna un gruppo di azioni |
> | Azione | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Avviso del log attività attivato |
> | Azione | Microsoft.Insights/ActivityLogAlerts/Delete | Elimina un avviso del log attività |
> | Azione | Microsoft.Insights/ActivityLogAlerts/Read | Legge un avviso del log attività |
> | Azione | Microsoft.Insights/ActivityLogAlerts/Write | Crea o aggiorna un avviso del log attività |
> | Azione | Microsoft.Insights/AlertRules/Activated/Action | Avviso della metrica attivato (versione classica) |
> | Azione | Microsoft.Insights/AlertRules/Delete | Elimina un avviso della metrica (versione classica) |
> | Azione | Microsoft.Insights/AlertRules/Incidents/Read | Legge un evento imprevisto di avviso della metrica (versione classica) |
> | Azione | Microsoft.Insights/AlertRules/Read | Legge un avviso della metrica (versione classica) |
> | Azione | Microsoft.Insights/AlertRules/Resolved/Action | Avviso della metrica risolto (versione classica) |
> | Azione | Microsoft.Insights/AlertRules/Throttled/Action | Regola di avviso della metrica limitata (versione classica) |
> | Azione | Microsoft.Insights/AlertRules/Write | Crea o aggiorna un avviso della metrica (versione classica) |
> | Azione | Microsoft.Insights/AutoscaleSettings/Delete | Elimina un'impostazione di scalabilità automatica |
> | Azione | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Legge un'impostazione di diagnostica della risorsa |
> | Azione | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Crea o aggiorna un'impostazione di diagnostica della risorsa |
> | Azione | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Consente di leggere le definizioni del log |
> | Azione | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | Azione | Microsoft.Insights/AutoscaleSettings/Read | Legge un'impostazione di scalabilità automatica |
> | Azione | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Scalabilità automatica: riduzione avviata |
> | Azione | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Scalabilità automatica: riduzione completata |
> | Azione | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Scalabilità automatica: aumento avviato |
> | Azione | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Scalabilità automatica: aumento completato |
> | Azione | Microsoft.Insights/AutoscaleSettings/Write | Crea o aggiorna un'impostazione di scalabilità automatica |
> | Azione | Microsoft. Insights/Baseline/lettura | Leggi una baseline metrica (anteprima) |
> | Azione | Microsoft.Insights/CalculateBaseline/Read | Calcolare la baseline per i valori delle metriche (anteprima) |
> | Azione | Microsoft.Insights/Components/AnalyticsItems/Delete | Eliminazione di un elemento di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/AnalyticsItems/Read | Lettura di un elemento di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/AnalyticsItems/Write | Scrittura di un elemento di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/AnalyticsTables/Action | Azione di tabella di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/AnalyticsTables/Delete | Eliminazione di uno schema tabella di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/AnalyticsTables/Read | Lettura di uno schema tabella di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/AnalyticsTables/Write | Scrittura di uno schema tabella di analisi di Application Insights |
> | Azione | Microsoft.Insights/Components/Annotations/Delete | Eliminazione di un'annotazione di Application Insights |
> | Azione | Microsoft.Insights/Components/Annotations/Read | Lettura di un'annotazione di Application Insights |
> | Azione | Microsoft.Insights/Components/Annotations/Write | Scrittura di un'annotazione di Application Insights |
> | Azione | Microsoft.Insights/Components/Api/Read | Lettura dell'API dei dati dei componenti di Application Insights |
> | Azione | Microsoft.Insights/Components/ApiKeys/Action | Generazione di una chiave API di Application Insights |
> | Azione | Microsoft.Insights/Components/ApiKeys/Delete | Eliminazione di una chiave API di Application Insights |
> | Azione | Microsoft.Insights/Components/ApiKeys/Read | Lettura di una chiave API di Application Insights |
> | Azione | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lettura di un piano di fatturazione per un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lettura delle funzioni di fatturazione correnti per un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Scrittura delle funzioni di fatturazione correnti per un componente di Application Insights |
> | Azione | Microsoft. Insights/Components/DailyCapReached/Action | È stato raggiunto il limite giornaliero per il componente Application Insights |
> | Azione | Microsoft. Insights/Components/DailyCapWarningThresholdReached/Action | È stata raggiunta la soglia di avviso limite giornaliero per il componente Application Insights |
> | Azione | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lettura di una configurazione di integrazione di ALM predefinita di Application Insights |
> | Azione | Microsoft.Insights/Components/Delete | Eliminazione della configurazione di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/Events/Read | Ottiene i log da Application Insights usando il formato di query OData |
> | Azione | Microsoft.Insights/Components/ExportConfiguration/Action | Azione di impostazioni di esportazione di Application Insights |
> | Azione | Microsoft.Insights/Components/ExportConfiguration/Delete | Eliminazione delle impostazioni di esportazione di Application Insights |
> | Azione | Microsoft.Insights/Components/ExportConfiguration/Read | Lettura delle impostazioni di esportazione di Application Insights |
> | Azione | Microsoft.Insights/Components/ExportConfiguration/Write | Scrittura delle impostazioni di esportazione di Application Insights |
> | Azione | Microsoft.Insights/Components/ExtendQueries/Read | Lettura dei risultati di query estesi del componente di Application Insights |
> | Azione | Microsoft.Insights/Components/Favorites/Delete | Eliminazione di un preferito di Application Insights |
> | Azione | Microsoft.Insights/Components/Favorites/Read | Lettura di un preferito di Application Insights |
> | Azione | Microsoft.Insights/Components/Favorites/Write | Scrittura di un preferito di Application Insights |
> | Azione | Microsoft.Insights/Components/FeatureCapabilities/Read | Lettura delle capacità delle funzioni di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lettura delle funzioni di fatturazione disponibili per un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/GetToken/Read | Lettura del token di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/MetricDefinitions/Read | Lettura delle definizioni della metrica di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/Metrics/Read | Lettura della metrica di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/Move/Action | Sposta un componente di Application Insights in un altro gruppo di risorse o sottoscrizione |
> | Azione | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Eliminazione di un elemento di analisi personale di Application Insights |
> | Azione | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lettura di un elemento di analisi personale di Application Insights |
> | Azione | Microsoft.Insights/Components/MyAnalyticsItems/Write | Scrittura di un elemento di analisi personale di Application Insights |
> | Azione | Microsoft.Insights/Components/MyFavorites/Read | Lettura di un preferito personale di Application Insights |
> | Azione | Microsoft.Insights/Components/Operations/Read | Ottiene lo stato delle operazioni a esecuzione prolungata in Application Insights |
> | Azione | Microsoft.Insights/Components/PricingPlans/Read | Lettura del piano di determinazione dei prezzi di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/PricingPlans/Write | Scrittura del piano di determinazione dei prezzi di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lettura della configurazione di rilevamento proattivo di Application Insights |
> | Azione | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Scrittura della configurazione di rilevamento proattivo di Application Insights |
> | Azione | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | Azione | Microsoft.Insights/Components/Purge/Action | Ripulisce i dati da Application Insights |
> | Azione | Microsoft.Insights/Components/Query/Read | Esegue query sui log di Application Insights |
> | Azione | Microsoft.Insights/Components/QuotaStatus/Read | Lettura dello stato delle quote di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/Read | Lettura della configurazione di un componente di Application Insights |
> | Azione | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lettura delle posizioni dei test Web di Application Insights |
> | Azione | Microsoft.Insights/Components/Webtests/Read | Lettura della configurazione di un test Web |
> | Azione | Microsoft.Insights/Components/WorkItemConfigs/Delete | Eliminazione di una configurazione di integrazione di ALM di Application Insights |
> | Azione | Microsoft.Insights/Components/WorkItemConfigs/Read | Lettura di una configurazione di integrazione di ALM di Application Insights |
> | Azione | Microsoft.Insights/Components/WorkItemConfigs/Write | Scrittura di una configurazione di integrazione di ALM di Application Insights |
> | Azione | Microsoft.Insights/Components/Write | Scrittura nella configurazione di un componente di Application Insights |
> | Azione | Microsoft. Insights/DataCollectionRuleAssociations/Delete | Eliminare l'associazione di una risorsa a una regola di raccolta dati |
> | Azione | Microsoft. Insights/DataCollectionRuleAssociations/lettura | Leggere l'associazione di una risorsa a una regola di raccolta dati |
> | Azione | Microsoft. Insights/DataCollectionRuleAssociations/scrittura | Creare o aggiornare l'associazione di una risorsa a una regola di raccolta dati |
> | DataAction | Microsoft. Insights/DataCollectionRules/data/scrittura | Inviare dati a una regola di raccolta dati |
> | Azione | Microsoft. Insights/DataCollectionRules/Delete | Eliminare una regola di raccolta dati |
> | Azione | Microsoft. Insights/DataCollectionRules/lettura | Leggi una regola di raccolta dati |
> | Azione | Microsoft. Insights/DataCollectionRules/scrittura | Creare o aggiornare una regola di raccolta dati |
> | Azione | Microsoft.Insights/DiagnosticSettings/Delete | Elimina un'impostazione di diagnostica della risorsa |
> | Azione | Microsoft.Insights/DiagnosticSettings/Read | Legge un'impostazione di diagnostica della risorsa |
> | Azione | Microsoft.Insights/DiagnosticSettings/Write | Crea o aggiorna un'impostazione di diagnostica della risorsa |
> | Azione | Microsoft.Insights/EventCategories/Read | Legge le categorie di eventi del log attività disponibili |
> | Azione | Microsoft.Insights/eventtypes/digestevents/Read | Consente di leggere il digest dei tipi di evento di gestione |
> | Azione | Microsoft.Insights/eventtypes/values/Read | Legge gli eventi del log attività |
> | Azione | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Elimina un'impostazione di diagnostica del log dei flussi di rete |
> | Azione | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Legge un'impostazione di diagnostica del log dei flussi di rete |
> | Azione | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Crea o aggiorna un'impostazione di diagnostica del log dei flussi di rete |
> | Azione | Microsoft.Insights/ListMigrationDate/Action | Riottiene la data di migrazione della sottoscrizione |
> | Azione | Microsoft.Insights/ListMigrationDate/Read | Riottiene la data di migrazione della sottoscrizione |
> | Azione | Microsoft.Insights/LogDefinitions/Read | Consente di leggere le definizioni del log |
> | Azione | Microsoft.Insights/LogProfiles/Delete | Elimina un profilo del log attività |
> | Azione | Microsoft.Insights/LogProfiles/Read | Legge un profilo del log attività |
> | Azione | Microsoft.Insights/LogProfiles/Write | Crea o aggiorna un profilo del log attività |
> | Azione | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Legge i dati dalla tabella ADAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/ADReplicationResult/Read | Legge i dati dalla tabella ADReplicationResult |
> | Azione | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Legge i dati dalla tabella ADSecurityAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/Alert/Read | Legge i dati dalla tabella Alert |
> | Azione | Microsoft.Insights/Logs/AlertHistory/Read | Legge i dati dalla tabella AlertHistory |
> | Azione | Microsoft.Insights/Logs/ApplicationInsights/Read | Legge i dati dalla tabella ApplicationInsights |
> | Azione | Microsoft.Insights/Logs/AzureActivity/Read | Legge i dati dalla tabella AzureActivity |
> | Azione | Microsoft.Insights/Logs/AzureMetrics/Read | Legge i dati dalla tabella AzureMetrics |
> | Azione | Microsoft.Insights/Logs/BoundPort/Read | Legge i dati dalla tabella BoundPort |
> | Azione | Microsoft.Insights/Logs/CommonSecurityLog/Read | Legge i dati dalla tabella CommonSecurityLog |
> | Azione | Microsoft.Insights/Logs/ComputerGroup/Read | Legge i dati dalla tabella ComputerGroup |
> | Azione | Microsoft.Insights/Logs/ConfigurationChange/Read | Legge i dati dalla tabella ConfigurationChange |
> | Azione | Microsoft.Insights/Logs/ConfigurationData/Read | Legge i dati dalla tabella ConfigurationData |
> | Azione | Microsoft.Insights/Logs/ContainerImageInventory/Read | Legge i dati dalla tabella ContainerImageInventory |
> | Azione | Microsoft.Insights/Logs/ContainerInventory/Read | Legge i dati dalla tabella ContainerInventory |
> | Azione | Microsoft.Insights/Logs/ContainerLog/Read | Legge i dati dalla tabella ContainerLog |
> | Azione | Microsoft.Insights/Logs/ContainerServiceLog/Read | Legge i dati dalla tabella ContainerServiceLog |
> | Azione | Microsoft.Insights/Logs/DeviceAppCrash/Read | Legge i dati dalla tabella DeviceAppCrash |
> | Azione | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Legge i dati dalla tabella DeviceAppLaunch |
> | Azione | Microsoft.Insights/Logs/DeviceCalendar/Read | Legge i dati dalla tabella DeviceCalendar |
> | Azione | Microsoft.Insights/Logs/DeviceCleanup/Read | Legge i dati dalla tabella DeviceCleanup |
> | Azione | Microsoft.Insights/Logs/DeviceConnectSession/Read | Legge i dati dalla tabella DeviceConnectSession |
> | Azione | Microsoft.Insights/Logs/DeviceEtw/Read | Legge i dati dalla tabella DeviceEtw |
> | Azione | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Legge i dati dalla tabella DeviceHardwareHealth |
> | Azione | Microsoft.Insights/Logs/DeviceHealth/Read | Legge i dati dalla tabella DeviceHealth |
> | Azione | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Legge i dati dalla tabella DeviceHeartbeat |
> | Azione | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Legge i dati dalla tabella DeviceSkypeHeartbeat |
> | Azione | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Legge i dati dalla tabella DeviceSkypeSignIn |
> | Azione | Microsoft.Insights/Logs/DeviceSleepState/Read | Legge i dati dalla tabella DeviceSleepState |
> | Azione | Microsoft.Insights/Logs/DHAppFailure/Read | Legge i dati dalla tabella DHAppFailure |
> | Azione | Microsoft.Insights/Logs/DHAppReliability/Read | Legge i dati dalla tabella DHAppReliability |
> | Azione | Microsoft.Insights/Logs/DHDriverReliability/Read | Legge i dati dalla tabella DHDriverReliability |
> | Azione | Microsoft.Insights/Logs/DHLogonFailures/Read | Legge i dati dalla tabella DHLogonFailures |
> | Azione | Microsoft.Insights/Logs/DHLogonMetrics/Read | Legge i dati dalla tabella DHLogonMetrics |
> | Azione | Microsoft.Insights/Logs/DHOSCrashData/Read | Legge i dati dalla tabella DHOSCrashData |
> | Azione | Microsoft.Insights/Logs/DHOSReliability/Read | Legge i dati dalla tabella DHOSReliability |
> | Azione | Microsoft.Insights/Logs/DHWipAppLearning/Read | Legge i dati dalla tabella DHWipAppLearning |
> | Azione | Microsoft.Insights/Logs/DnsEvents/Read | Legge i dati dalla tabella DnsEvents |
> | Azione | Microsoft.Insights/Logs/DnsInventory/Read | Legge i dati dalla tabella DnsInventory |
> | Azione | Microsoft.Insights/Logs/ETWEvent/Read | Legge i dati dalla tabella ETWEvent |
> | Azione | Microsoft.Insights/Logs/Event/Read | Legge i dati dalla tabella Event |
> | Azione | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Legge i dati dalla tabella ExchangeAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Legge i dati dalla tabella ExchangeOnlineAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/Heartbeat/Read | Legge i dati dalla tabella Heartbeat |
> | Azione | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Legge i dati dalla tabella IISAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/InboundConnection/Read | Legge i dati dalla tabella InboundConnection |
> | Azione | Microsoft.Insights/Logs/KubeNodeInventory/Read | Legge i dati dalla tabella KubeNodeInventory |
> | Azione | Microsoft.Insights/Logs/KubePodInventory/Read | Legge i dati dalla tabella KubePodInventory |
> | Azione | Microsoft.Insights/Logs/LinuxAuditLog/Read | Legge i dati dalla tabella LinuxAuditLog |
> | Azione | Microsoft.Insights/Logs/MAApplication/Read | Legge i dati dalla tabella MAApplication |
> | Azione | Microsoft.Insights/Logs/MAApplicationHealth/Read | Legge i dati dalla tabella MAApplicationHealth |
> | Azione | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Legge i dati dalla tabella MAApplicationHealthAlternativeVersions |
> | Azione | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Legge i dati dalla tabella MAApplicationHealthIssues |
> | Azione | Microsoft.Insights/Logs/MAApplicationInstance/Read | Legge i dati dalla tabella MAApplicationInstance |
> | Azione | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Legge i dati dalla tabella MAApplicationInstanceReadiness |
> | Azione | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Legge i dati dalla tabella MAApplicationReadiness |
> | Azione | Microsoft.Insights/Logs/MADeploymentPlan/Read | Legge i dati dalla tabella MADeploymentPlan |
> | Azione | Microsoft.Insights/Logs/MADevice/Read | Legge i dati dalla tabella MADevice |
> | Azione | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Legge i dati dalla tabella MADevicePnPHealth |
> | Azione | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Legge i dati dalla tabella MADevicePnPHealthAlternativeVersions |
> | Azione | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Legge i dati dalla tabella MADevicePnPHealthIssues |
> | Azione | Microsoft.Insights/Logs/MADeviceReadiness/Read | Legge i dati dalla tabella MADeviceReadiness |
> | Azione | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Legge i dati dalla tabella MADriverInstanceReadiness |
> | Azione | Microsoft.Insights/Logs/MADriverReadiness/Read | Legge i dati dalla tabella MADriverReadiness |
> | Azione | Microsoft.Insights/Logs/MAOfficeAddin/Read | Legge i dati dalla tabella MAOfficeAddin |
> | Azione | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Legge i dati dalla tabella MAOfficeAddinHealth |
> | Azione | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Legge i dati dalla tabella MAOfficeAddinHealthIssues |
> | Azione | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Legge i dati dalla tabella MAOfficeAddinInstance |
> | Azione | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Legge i dati dalla tabella MAOfficeAddinInstanceReadiness |
> | Azione | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Legge i dati dalla tabella MAOfficeAddinReadiness |
> | Azione | Microsoft.Insights/Logs/MAOfficeApp/Read | Legge i dati dalla tabella MAOfficeApp |
> | Azione | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Legge i dati dalla tabella MAOfficeAppHealth |
> | Azione | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Legge i dati dalla tabella MAOfficeAppInstance |
> | Azione | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Legge i dati dalla tabella MAOfficeAppReadiness |
> | Azione | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Legge i dati dalla tabella MAOfficeBuildInfo |
> | Azione | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Legge i dati dalla tabella MAOfficeCurrencyAssessment |
> | Azione | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Legge i dati dalla tabella MAOfficeCurrencyAssessmentDailyCounts |
> | Azione | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Legge i dati dalla tabella MAOfficeDeploymentStatus |
> | Azione | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Legge i dati dalla tabella MAOfficeMacroHealth |
> | Azione | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Legge i dati dalla tabella MAOfficeMacroHealthIssues |
> | Azione | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Legge i dati dalla tabella MAOfficeMacroIssueInstanceReadiness |
> | Azione | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Legge i dati dalla tabella MAOfficeMacroIssueReadiness |
> | Azione | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Legge i dati dalla tabella MAOfficeMacroSummary |
> | Azione | Microsoft.Insights/Logs/MAOfficeSuite/Read | Legge i dati dalla tabella MAOfficeSuite |
> | Azione | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Legge i dati dalla tabella MAOfficeSuiteInstance |
> | Azione | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Legge i dati dalla tabella MAProposedPilotDevices |
> | Azione | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Legge i dati dalla tabella MAWindowsBuildInfo |
> | Azione | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Legge i dati dalla tabella MAWindowsCurrencyAssessment |
> | Azione | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Legge i dati dalla tabella MAWindowsCurrencyAssessmentDailyCounts |
> | Azione | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Legge i dati dalla tabella MAWindowsDeploymentStatus |
> | Azione | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Legge i dati dalla tabella MAWindowsSysReqInstanceReadiness |
> | Azione | Microsoft.Insights/Logs/NetworkMonitoring/Read | Legge i dati dalla tabella NetworkMonitoring |
> | Azione | Microsoft.Insights/Logs/OfficeActivity/Read | Legge i dati dalla tabella OfficeActivity |
> | Azione | Microsoft.Insights/Logs/Operation/Read | Legge i dati dalla tabella Operation |
> | Azione | Microsoft.Insights/Logs/OutboundConnection/Read | Legge i dati dalla tabella OutboundConnection |
> | Azione | Microsoft.Insights/Logs/Perf/Read | Legge i dati dalla tabella Perf |
> | Azione | Microsoft.Insights/Logs/ProtectionStatus/Read | Legge i dati dalla tabella ProtectionStatus |
> | Azione | Microsoft.Insights/Logs/Read | Legge i dati da tutti i log |
> | Azione | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Legge i dati dalla tabella ReservedAzureCommonFields |
> | Azione | Microsoft.Insights/Logs/ReservedCommonFields/Read | Legge i dati dalla tabella ReservedCommonFields |
> | Azione | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Legge i dati dalla tabella SCCMAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Legge i dati dalla tabella SCOMAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SecurityAlert/Read | Legge i dati dalla tabella SecurityAlert |
> | Azione | Microsoft.Insights/Logs/SecurityBaseline/Read | Legge i dati dalla tabella SecurityBaseline |
> | Azione | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Legge i dati dalla tabella SecurityBaselineSummary |
> | Azione | Microsoft.Insights/Logs/SecurityDetection/Read | Legge i dati dalla tabella SecurityDetection |
> | Azione | Microsoft.Insights/Logs/SecurityEvent/Read | Legge i dati dalla tabella SecurityEvent |
> | Azione | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Legge i dati dalla tabella ServiceFabricOperationalEvent |
> | Azione | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Legge i dati dalla tabella ServiceFabricReliableActorEvent |
> | Azione | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Legge i dati dalla tabella ServiceFabricReliableServiceEvent |
> | Azione | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Legge i dati dalla tabella SfBAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Legge i dati dalla tabella SfBOnlineAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Legge i dati dalla tabella SharePointOnlineAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Legge i dati dalla tabella SPAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Legge i dati dalla tabella SQLAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Legge i dati dalla tabella SQLQueryPerformance |
> | Azione | Microsoft.Insights/Logs/Syslog/Read | Legge i dati dalla tabella Syslog |
> | Azione | Microsoft.Insights/Logs/SysmonEvent/Read | Legge i dati dalla tabella SysmonEvent |
> | Azione | Microsoft. Insights/logs/Tables. Custom/Read | Legge i dati da qualsiasi log personalizzato |
> | Azione | Microsoft.Insights/Logs/UAApp/Read | Legge i dati dalla tabella UAApp |
> | Azione | Microsoft.Insights/Logs/UAComputer/Read | Legge i dati dalla tabella UAComputer |
> | Azione | Microsoft.Insights/Logs/UAComputerRank/Read | Legge i dati dalla tabella UAComputerRank |
> | Azione | Microsoft.Insights/Logs/UADriver/Read | Legge i dati dalla tabella UADriver |
> | Azione | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Legge i dati dalla tabella UADriverProblemCodes |
> | Azione | Microsoft.Insights/Logs/UAFeedback/Read | Legge i dati dalla tabella UAFeedback |
> | Azione | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Legge i dati dalla tabella UAHardwareSecurity |
> | Azione | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Legge i dati dalla tabella UAIESiteDiscovery |
> | Azione | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Legge i dati dalla tabella UAOfficeAddIn |
> | Azione | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Leggere dati dalla tabella UAProposedActionPlan |
> | Azione | Microsoft.Insights/Logs/UASysReqIssue/Read | Legge i dati dalla tabella UASysReqIssue |
> | Azione | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Legge i dati dalla tabella UAUpgradedComputer |
> | Azione | Microsoft.Insights/Logs/Update/Read | Legge i dati dalla tabella Update |
> | Azione | Microsoft.Insights/Logs/UpdateRunProgress/Read | Legge i dati dalla tabella UpdateRunProgress |
> | Azione | Microsoft.Insights/Logs/UpdateSummary/Read | Legge i dati dalla tabella UpdateSummary |
> | Azione | Microsoft.Insights/Logs/Usage/Read | Legge i dati dalla tabella Usage |
> | Azione | Microsoft.Insights/Logs/W3CIISLog/Read | Legge i dati dalla tabella W3CIISLog |
> | Azione | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Legge i dati dalla tabella WaaSDeploymentStatus |
> | Azione | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Legge i dati dalla tabella WaaSInsiderStatus |
> | Azione | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Legge i dati dalla tabella WaaSUpdateStatus |
> | Azione | Microsoft.Insights/Logs/WDAVStatus/Read | Legge i dati dalla tabella WDAVStatus |
> | Azione | Microsoft.Insights/Logs/WDAVThreat/Read | Legge i dati dalla tabella WDAVThreat |
> | Azione | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Legge i dati dalla tabella WindowsClientAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/WindowsFirewall/Read | Legge i dati dalla tabella WindowsFirewall |
> | Azione | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Legge i dati dalla tabella WindowsServerAssessmentRecommendation |
> | Azione | Microsoft.Insights/Logs/WireData/Read | Legge i dati dalla tabella WireData |
> | Azione | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Legge i dati dalla tabella WUDOAggregatedStatus |
> | Azione | Microsoft.Insights/Logs/WUDOStatus/Read | Legge i dati dalla tabella WUDOStatus |
> | Azione | Microsoft.Insights/MetricAlerts/Delete | Elimina un avviso della metrica |
> | Azione | Microsoft.Insights/MetricAlerts/Read | Legge un avviso della metrica |
> | Azione | Microsoft.Insights/MetricAlerts/Status/Read | Legge lo stato dell'avviso della metrica |
> | Azione | Microsoft.Insights/MetricAlerts/Write | Crea o aggiorna un avviso della metrica |
> | Azione | Microsoft.Insights/MetricBaselines/Read | Leggi Baseline metrica |
> | Azione | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Consente di leggere le definizioni della metrica |
> | Azione | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Consente di leggere le definizioni della metrica |
> | Azione | Microsoft.Insights/MetricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | Azione | Microsoft. Insights/Metricnamespaces/lettura | Leggi spazi dei nomi metrica |
> | Azione | Microsoft.Insights/Metrics/Action | Azione della metrica |
> | Azione | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Esegue la lettura delle metriche |
> | Azione | Microsoft.Insights/Metrics/providers/Metrics/Read | Esegue la lettura delle metriche |
> | Azione | Microsoft.Insights/Metrics/Read | Esegue la lettura delle metriche |
> | DataAction | Microsoft.Insights/Metrics/Write | Scrive metrica |
> | Azione | Microsoft.Insights/MigrateToNewpricingModel/Action | Migra la sottoscrizione a un nuovo modello di determinazione dei prezzi |
> | Azione | Microsoft.Insights/Operations/Read | Operazioni di lettura |
> | Azione | Microsoft.Insights/Register/Action | Registra il provider Microsoft Insights |
> | Azione | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Ripristino del modello di determinazione dei prezzi precedente di una sottoscrizione |
> | Azione | Microsoft.Insights/ScheduledQueryRules/Delete | Eliminazione di una regola di query pianificata |
> | Azione | Microsoft.Insights/ScheduledQueryRules/Read | Lettura di una regola di query pianificata |
> | Azione | Microsoft.Insights/ScheduledQueryRules/Write | Scrittura di una regola di query pianificata |
> | Azione | Microsoft.Insights/Tenants/Register/Action | Inizializza il provider Microsoft Insights |
> | Azione | Microsoft.Insights/Unregister/Action | Registra il provider Microsoft Insights |
> | Azione | Microsoft.Insights/Webtests/Delete | Eliminazione della configurazione di un test Web |
> | Azione | Microsoft.Insights/Webtests/GetToken/Read | Lettura di un token di test Web |
> | Azione | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lettura delle definizioni della metrica un test Web |
> | Azione | Microsoft.Insights/Webtests/Metrics/Read | Lettura della metrica di un test Web |
> | Azione | Microsoft.Insights/Webtests/Read | Lettura della configurazione di un test Web |
> | Azione | Microsoft.Insights/Webtests/Write | Scrittura della configurazione di un test Web |
> | Azione | Microsoft. Insights/cartelle di lavoro/eliminazione | Eliminare una cartella di lavoro |
> | Azione | Microsoft. Insights/cartelle di lavoro/lettura | Lettura di una cartella di lavoro |
> | Azione | Microsoft. Insights/cartelle di lavoro/scrittura | Creazione o aggiornamento di una cartella di lavoro |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Intune/diagnosticsettings/delete | Eliminazione di un'impostazione di diagnostica |
> | Azione | Microsoft.Intune/diagnosticsettings/read | Lettura di un'impostazione di diagnostica |
> | Azione | Microsoft.Intune/diagnosticsettings/write | Scrittura di un'impostazione di diagnostica |
> | Azione | Microsoft.Intune/diagnosticsettingscategories/read | Lettura delle categorie di un'impostazione di diagnostica |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.IoTCentral/appTemplates/action | Ottiene tutti i modelli di applicazione disponibili in Azure IoT Central |
> | Azione | Microsoft.IoTCentral/checkNameAvailability/action | Verifica se un nome di applicazione IoT Central è disponibile |
> | Azione | Microsoft.IoTCentral/checkSubdomainAvailability/action | Verifica se un sottodominio di applicazione IoT Central è disponibile |
> | Azione | Microsoft.IoTCentral/IoTApps/delete | Elimina applicazioni IoT Central |
> | Azione | Microsoft.IoTCentral/IoTApps/read | Recupera un’applicazione IoT Central singola |
> | Azione | Microsoft.IoTCentral/IoTApps/write | Crea o aggiorna applicazioni IoT Central |
> | Azione | Microsoft.IoTCentral/operations/read | Ottiene tutte le operazioni disponibili sulle applicazioni di IoT Central |
> | Azione | Microsoft.IoTCentral/register/action | Registra la sottoscrizione per il provider di risorse di Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.IoTSpaces/Graph/delete | Elimina la risorsa grafico Microsoft.IoTSpaces |
> | Azione | Microsoft.IoTSpaces/Graph/read | Ottiene le risorse grafico Microsoft.IoTSpaces |
> | Azione | Microsoft.IoTSpaces/Graph/write | Crea una risorsa grafico Microsoft.IoTSpaces |
> | Azione | Microsoft.IoTSpaces/register/action | Registra la sottoscrizione per il provider di risorse Microsoft.IoTSpaces Graph per consentire la creazione di risorse |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.KeyVault/checkNameAvailability/read | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | Azione | Microsoft.KeyVault/deletedVaults/read | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | Azione | Microsoft.KeyVault/hsmPools/delete | Elimina un pool di moduli di protezione hardware |
> | Azione | Microsoft.KeyVault/hsmPools/joinVault/action | Aggiunge un insieme di credenziali delle chiavi a un pool di moduli di protezione hardware |
> | Azione | Microsoft.KeyVault/hsmPools/read | Visualizza le proprietà di un pool di moduli di protezione hardware |
> | Azione | Microsoft.KeyVault/hsmPools/write | Crea un nuovo pool di moduli di protezione hardware o aggiorna le proprietà di un pool di moduli di protezione hardware esistente |
> | Azione | Microsoft.KeyVault/locations/deletedVaults/purge/action | Ripulisce un Key Vault eliminato temporaneamente |
> | Azione | Microsoft.KeyVault/locations/deletedVaults/read | Visualizza le proprietà di un Key Vault eliminato temporaneamente |
> | Azione | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica a Microsoft.KeyVault che è in corso l'eliminazione di una rete virtuale o di una subnet |
> | Azione | Microsoft.KeyVault/locations/operationResults/read | Controlla il risultato di un'operazione a esecuzione prolungata |
> | Azione | Microsoft.KeyVault/operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | Azione | Microsoft.KeyVault/register/action | Registra una sottoscrizione |
> | Azione | Microsoft.KeyVault/unregister/action | Annulla la registrazione di una sottoscrizione |
> | Azione | Microsoft.KeyVault/vaults/accessPolicies/write | Aggiorna un criterio di accesso esistente unendo o sostituendo oppure aggiungendo un nuovo criterio di accesso a un insieme di credenziali. |
> | Azione | Microsoft.KeyVault/vaults/delete | Elimina un insieme di credenziali delle chiavi |
> | Azione | Microsoft.KeyVault/vaults/deploy/action | Consente l'accesso ai segreti in un insieme di credenziali delle chiavi durante la distribuzione di risorse di Azure |
> | Azione | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifica a Microsoft. Vault che è in corso l'eliminazione di una sottoscrizione di EventGrid per Key Vault |
> | Azione | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifica a Microsoft. Vault che è in corso la visualizzazione di una sottoscrizione EventGrid per Key Vault |
> | Azione | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifica a Microsoft. Vault che è in corso la creazione di una nuova sottoscrizione EventGrid per Key Vault |
> | Azione | Microsoft.KeyVault/vaults/read | Visualizza le proprietà di un insieme di credenziali delle chiavi |
> | Azione | Microsoft.KeyVault/vaults/secrets/read | Visualizzare le proprietà di un segreto ma non il relativo valore. |
> | Azione | Microsoft.KeyVault/vaults/secrets/write | Crea un nuovo segreto o aggiorna il valore di un segreto esistente. |
> | Azione | Microsoft.KeyVault/vaults/write | Crea un nuovo insieme di credenziali delle chiavi o aggiorna le proprietà di un insieme di credenziali delle chiavi esistente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Kusto/Clusters/Activate/action | Avvia il cluster. |
> | Azione | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Elimina un resourceCopy di configurazione del database collegato. |
> | Azione | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Legge una configurazione del database associata resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Scrive una configurazione del database associata resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Controlla la disponibilità del nome del cluster. |
> | Azione | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Aggiunge entità di database. |
> | Azione | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Controlla la disponibilità dei nomi per un tipo specificato. |
> | Azione | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Elimina le connessioni dati resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Legge una connessione dati resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Scrive una connessione dati resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Convalida la connessione dati del database. |
> | Azione | Microsoft.Kusto/Clusters/Databases/delete | Elimina un database resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Elimina le connessioni di hub eventi resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Legge le connessioni di un hub eventi resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Scrive una connessione dell'hub eventi resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Convalida la connessione dell'hub eventi di database. |
> | Azione | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Elenca le entità di database. |
> | Azione | Microsoft. kusto/Clusters/databases/PrincipalAssignments/Delete | Elimina le assegnazioni di entità di database resourceCopy. |
> | Azione | Microsoft. kusto/Clusters/databases/PrincipalAssignments/Read | Legge le assegnazioni di entità di database resourceCopy. |
> | Azione | Microsoft. kusto/Clusters/databases/PrincipalAssignments/Write | Scrive le assegnazioni di entità di database resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Databases/read | Legge un resourceCopy del database. |
> | Azione | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Rimuove le entità di database. |
> | Azione | Microsoft.Kusto/Clusters/Databases/write | Scrive un database resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Deactivate/action | Arresta il cluster. |
> | Azione | Microsoft.Kusto/Clusters/delete | Elimina un resourceCopy del cluster. |
> | Azione | Microsoft. kusto/Clusters/DetachFollowerDatabases/Action | Scollega i database del follower. |
> | Azione | Microsoft. kusto/Clusters/DiagnoseVirtualNetwork/Action | Diagnostica lo stato della connettività di rete per le risorse esterne su cui è depedent il servizio. |
> | Azione | Microsoft. kusto/Clusters/ListFollowerDatabases/Action | Elenca i database del follower. |
> | Azione | Microsoft.Kusto/Clusters/read | Legge un cluster resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/SKUs/read | Legge uno SKU del cluster resourceCopy. |
> | Azione | Microsoft.Kusto/Clusters/Start/action | Avvia il cluster. |
> | Azione | Microsoft.Kusto/Clusters/Stop/action | Arresta il cluster. |
> | Azione | Microsoft.Kusto/Clusters/write | Scrive un cluster resourceCopy. |
> | Azione | Microsoft.Kusto/Locations/CheckNameAvailability/action | Controlla la disponibilità del nome resourceCopy. |
> | Azione | Microsoft. kusto/locations/GetNetworkPolicies/Action | Ottiene i criteri per finalità di rete |
> | Azione | Microsoft.Kusto/locations/operationresults/read | Operazioni di lettura resourceCopys |
> | Azione | Microsoft.Kusto/Operations/read | Operazioni di lettura resourceCopys |
> | Azione | Microsoft. kusto/Register/Action | Azione di registrazione della sottoscrizione |
> | Azione | Microsoft.Kusto/Register/action | Registra la sottoscrizione per il provider di risorse kusto. |
> | Azione | Microsoft.Kusto/SKUs/read | Legge uno SKU resourceCopy. |
> | Azione | Microsoft.Kusto/Unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.LabServices/labAccounts/CreateLab/action | Crea un lab in un account di Lab. |
> | Azione | Microsoft.LabServices/labAccounts/delete | Elimina gli account del lab. |
> | Azione | Microsoft.LabServices/labAccounts/galleryImages/delete | Elimina le immagini della raccolta. |
> | Azione | Microsoft.LabServices/labAccounts/galleryImages/read | Esegue la lettura delle immagini della raccolta. |
> | Azione | Microsoft.LabServices/labAccounts/galleryImages/write | Aggiunge o modifica le immagini della raccolta. |
> | Azione | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Ottiene informazioni sulla disponibilità a livello di area per ogni categoria di dimensioni configurata in un account del lab |
> | Azione | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Aggiungere utenti a un lab |
> | Azione | Microsoft.LabServices/labAccounts/labs/delete | Elimina lab. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Elimina un'impostazione di ambiente. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Elimina ambienti. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Esegue la lettura di ambienti. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Reimposta la password dell'utente in un ambiente |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Avvia un ambiente avviando tutte le risorse all'interno di esso. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Arresta un ambiente arrestando tutte le risorse all'interno di esso. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Aggiunge o modifica ambienti. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Effettua il provisioning/deprovisioning delle risorse necessarie per un'impostazione di ambiente in base allo stato corrente dell'impostazione di lab/ambiente. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Legge un'impostazione di ambiente. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Reimposta la password nella macchina virtuale del modello. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Salva l'immagine modello corrente nella raccolta condivisa nell'account Lab |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Elimina le pianificazioni. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Esegue la lettura delle pianificazioni. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Aggiunge o modifica pianificazioni. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Avvia un modello avviando tutte le risorse all'interno di esso. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Arresta un modello arrestando tutte le risorse all'interno del modello. |
> | Azione | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Aggiunge o modifica un'impostazione di ambiente. |
> | Azione | Microsoft.LabServices/labAccounts/labs/read | Esegue la lettura di lab. |
> | Azione | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Invia un messaggio di posta elettronica contenente il collegamento per la registrazione al lab |
> | Azione | Microsoft.LabServices/labAccounts/labs/users/delete | Elimina gli utenti. |
> | Azione | Microsoft.LabServices/labAccounts/labs/users/read | Legge utenti. |
> | Azione | Microsoft.LabServices/labAccounts/labs/users/write | Aggiunge o modifica gli utenti. |
> | Azione | Microsoft.LabServices/labAccounts/labs/write | Aggiunge o modifica lab. |
> | Azione | Microsoft.LabServices/labAccounts/read | Legge gli account del lab. |
> | Azione | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Elimina sharedgalleries. |
> | Azione | Microsoft.LabServices/labAccounts/sharedGalleries/read | Legge sharedgalleries. |
> | Azione | Microsoft.LabServices/labAccounts/sharedGalleries/write | Aggiunge o modifica sharedgalleries. |
> | Azione | Microsoft.LabServices/labAccounts/sharedImages/delete | Elimina sharedimages. |
> | Azione | Microsoft.LabServices/labAccounts/sharedImages/read | Legge sharedimages. |
> | Azione | Microsoft.LabServices/labAccounts/sharedImages/write | Aggiunge o modifica sharedimages. |
> | Azione | Microsoft.LabServices/labAccounts/write | Aggiunge o modifica gli account del lab. |
> | Azione | Microsoft.LabServices/locations/operations/read | Esegue la lettura delle operazioni. |
> | Azione | Microsoft.LabServices/register/action | Registra la sottoscrizione |
> | Azione | Microsoft.LabServices/users/ListAllEnvironments/action | Elencare tutti gli ambienti per l'utente |
> | Azione | Microsoft.LabServices/users/Register/action | Effettua la registrazione di un utente a un lab gestito. |
> | Azione | Microsoft.LabServices/users/ResetPassword/action | Reimposta la password dell'utente in un ambiente |
> | Azione | Microsoft.LabServices/users/StartEnvironment/action | Avvia un ambiente avviando tutte le risorse all'interno di esso. |
> | Azione | Microsoft.LabServices/users/StopEnvironment/action | Arresta un ambiente arrestando tutte le risorse all'interno di esso. |
> | Azione | Microsoft. LabServices/Users/UserSettings/Action | Aggiorna e restituisce le impostazioni utente personali. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Logic/integrationAccounts/agreements/delete | Elimina il contratto nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Ottiene l'URL di callback per il contenuto del contratto nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/agreements/read | Legge il contratto nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/agreements/write | Crea o aggiorna il contratto nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/assemblies/delete | Elimina l'assembly nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Ottiene l'URL di callback per il contenuto dell'assembly nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/assemblies/read | Legge l'assembly nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/assemblies/write | Crea o aggiorna l'assembly nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Elimina la configurazione di batch nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Legge la configurazione di batch nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Crea o aggiorna la configurazione di batch nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/certificates/delete | Elimina il certificato nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/certificates/read | Legge il certificato nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/certificates/write | Crea o aggiorna il certificato nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/delete | Elimina l'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/join/action | Aggiunge l'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Ottiene l'URL di callback per l'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Ottiene le chiavi nell'insieme di credenziali delle chiavi. |
> | Azione | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registra gli eventi di verifica nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/maps/delete | Elimina la mappa nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Ottiene l'URL di callback per il contenuto della mappa nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/maps/read | Legge la mappa nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/maps/write | Crea o aggiorna la mappa nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/partners/delete | Elimina il partner nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Ottiene l'URL di callback per il contenuto del partner nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/partners/read | Legge il partner nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/partners/write | Crea o aggiorna il partner nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Legge le definizioni dei log dell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/read | Legge l'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Rigenera i segreti della chiave di accesso. |
> | Azione | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Delete | Elimina la configurazione del processo RosettaNet nell'account di integrazione. |
> | Azione | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Read | Legge la configurazione del processo RosettaNet nell'account di integrazione. |
> | Azione | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Write | Crea o aggiorna la configurazione del processo RosettaNet nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/schemas/delete | Elimina lo schema nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Ottiene l'URL di callback per il contenuto dello schema nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/schemas/read | Legge lo schema nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/schemas/write | Crea o aggiorna lo schema nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/sessions/delete | Elimina la sessione nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/sessions/read | Legge la configurazione di batch nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/sessions/write | Crea o aggiorna la sessione nell'account di integrazione. |
> | Azione | Microsoft.Logic/integrationAccounts/write | Crea o aggiorna l'account di integrazione. |
> | Azione | Microsoft. Logic/integrationServiceEnvironments/availableManagedApis/Read | Legge le API gestite disponibili nell'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/delete | Elimina l'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/join/action | Aggiunge l'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Esegue la lettura dell'operazione API gestita dall'ambiente del servizio di integrazione. |
> | Azione | Microsoft. Logic/integrationServiceEnvironments/managedApis/join/azione | Aggiunge l'API gestita ambiente del servizio di integrazione. |
> | Azione | Microsoft. Logic/integrationServiceEnvironments/managedApis/operationStatuses/Read | Legge gli Stati dell'operazione API gestita dell'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Esegue la lettura dell'API gestita dall'ambiente del servizio di integrazione. |
> | Azione | Microsoft. Logic/integrationServiceEnvironments/managedApis/Write | Crea o aggiorna l'API gestita dell'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Esegue la lettura degli stati delle operazioni dell'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Esegue la lettura delle definizioni delle metriche dell'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/read | Legge l'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/integrationServiceEnvironments/write | Crea o aggiorna l'ambiente del servizio di integrazione. |
> | Azione | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Recupera i gruppi di operazioni consigliate per il flusso di lavoro. |
> | Azione | Microsoft.Logic/locations/workflows/validate/action | Convalida il flusso di lavoro. |
> | Azione | Microsoft.Logic/operations/read | Ottiene l'operazione. |
> | Azione | Microsoft.Logic/register/action | Registra il provider di risorse Microsoft.Logic per una sottoscrizione specifica. |
> | Azione | Microsoft.Logic/workflows/accessKeys/delete | Elimina la chiave di accesso. |
> | Azione | Microsoft.Logic/workflows/accessKeys/list/action | Elenca i segreti della chiave di accesso. |
> | Azione | Microsoft.Logic/workflows/accessKeys/read | Esegue la lettura della chiave di accesso. |
> | Azione | Microsoft.Logic/workflows/accessKeys/regenerate/action | Rigenera i segreti della chiave di accesso. |
> | Azione | Microsoft.Logic/workflows/accessKeys/write | Crea una chiave di accesso o ne aggiorna una esistente. |
> | Azione | Microsoft.Logic/workflows/delete | Elimina il flusso di lavoro. |
> | Azione | Microsoft. Logic/workflows/Detectors/lettura | Legge il rilevatore del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/disable/action | Disabilita il flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/enable/action | Abilita il flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/listCallbackUrl/action | Ottiene l'URL di callback per il flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/listSwagger/action | Ottiene le definizioni di Swagger del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/move/action | Sposta il flusso di lavoro dall'ID sottoscrizione, dal gruppo di risorse e/o dal nome esistente a un ID sottoscrizione, a un gruppo di risorse e/o a un nome diverso. |
> | Azione | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Legge le impostazioni di diagnostica del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Legge le definizioni dei log del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Legge definizioni della metrica del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/read | Esegue la lettura del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/regenerateAccessKey/action | Rigenera i segreti della chiave di accesso. |
> | Azione | Microsoft.Logic/workflows/run/action | Avvia l'esecuzione di un flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Ottiene le tracce dell'espressione dell'azione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/read | Esegue la lettura dell'azione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Ottiene le tracce dell'espressione della ripetizione dell'azione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/repetitions/read | Legge la ripetizione dell'azione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Legge la cronologia della richiesta dell'azione di ripetizione dell'esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Legge la cronologia della richiesta dell'azione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Legge la ripetizione dell'ambito dell'azione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/cancel/action | Annulla l'esecuzione di un flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/delete | Elimina un'esecuzione di un flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/operations/read | Esegue la lettura dello stato dell'operazione di esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/runs/read | Esegue la lettura dell'esecuzione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/suspend/action | Sospende il flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/triggers/histories/read | Esegue la lettura delle cronologie del trigger. |
> | Azione | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Invia di nuovo il trigger del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Ottiene l'URL di callback per il trigger. |
> | Azione | Microsoft.Logic/workflows/triggers/read | Esegue la lettura del trigger. |
> | Azione | Microsoft.Logic/workflows/triggers/reset/action | Reimposta il trigger. |
> | Azione | Microsoft.Logic/workflows/triggers/run/action | Esegue il trigger. |
> | Azione | Microsoft.Logic/workflows/triggers/setState/action | Imposta lo stato del trigger. |
> | Azione | Microsoft.Logic/workflows/validate/action | Convalida il flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/versions/read | Esegue la lettura della versione del flusso di lavoro. |
> | Azione | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Ottiene l'URL di callback per il trigger. |
> | Azione | Microsoft.Logic/workflows/write | Crea un flusso di lavoro o ne aggiorna uno esistente. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Sposta qualsiasi associazione a un piano di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Esegue la lettura di qualsiasi associazione a un piano di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/commitmentPlans/delete | Elimina un qualsiasi piano di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/commitmentPlans/join/action | Consente di partecipare a qualsiasi piano di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/commitmentPlans/read | Esegue la lettura di qualsiasi piano di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/commitmentPlans/write | Crea o aggiorna un qualsiasi piano di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/locations/operationresults/read | Ottiene il risultato di un'operazione di Machine Learning |
> | Azione | Microsoft.MachineLearning/locations/operationsstatus/read | Ottiene lo stato di un'operazione di Machine Learning in corso |
> | Azione | Microsoft.MachineLearning/operations/read | Ottiene le operazioni di Machine Learning |
> | Azione | Microsoft.MachineLearning/register/action | Registra la sottoscrizione per il provider di risorse del servizio Web di Machine Learning e consente la creazione di servizi Web. |
> | Azione | Microsoft.MachineLearning/skus/read | Ottiene gli SKU dei piani di impegno di Machine Learning |
> | Azione | Microsoft.MachineLearning/webServices/action | Creare proprietà del servizio Web regionali per le aree supportate |
> | Azione | Microsoft.MachineLearning/webServices/delete | Elimina qualsiasi servizio Web di Machine Learning. |
> | Azione | Microsoft.MachineLearning/webServices/listkeys/read | Recupera le chiavi per un servizio Web di Machine Learning. |
> | Azione | Microsoft.MachineLearning/webServices/read | Legge qualsiasi servizio Web di Machine Learning. |
> | Azione | Microsoft.MachineLearning/webServices/write | Crea o aggiorna qualsiasi servizio Web di Machine Learning. |
> | Azione | Microsoft.MachineLearning/Workspaces/delete | Elimina una qualsiasi area di lavoro di Machine Learning |
> | Azione | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Elenca le chiavi per un'area di lavoro di Machine Learning |
> | Azione | Microsoft.MachineLearning/Workspaces/read | Esegue la lettura di una qualsiasi area di lavoro di Machine Learning |
> | Azione | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Risincronizza le chiavi dell'account di archiviazione configurato per un'area di lavoro di Machine Learning |
> | Azione | Microsoft.MachineLearning/Workspaces/write | Crea o aggiorna una qualsiasi area di lavoro di Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Ottiene lo stato di un'operazione di calcolo particolare |
> | Azione | Microsoft. MachineLearningServices/locations/quotas/Read | Ottiene le quote dell'area di lavoro attualmente assegnate in base a VMFamily. |
> | Azione | Microsoft. MachineLearningServices/locations/updateQuotas/Action | Aggiornare la quota per ogni famiglia di VM nell'area di lavoro. |
> | Azione | Microsoft.MachineLearningServices/locations/usages/read | Report di utilizzo per le risorse di calcolo di Azure Machine Learning in una sottoscrizione |
> | Azione | Microsoft.MachineLearningServices/locations/vmsizes/read | Ottiene le dimensioni delle macchine virtuali supportate |
> | Azione | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Ottiene lo stato di un'operazione sull'area di lavoro particolare |
> | Azione | Microsoft.MachineLearningServices/register/action | Registra la sottoscrizione per il provider di risorse di Machine Learning Services |
> | Azione | Microsoft.MachineLearningServices/workspaces/computes/delete | Elimina le risorse di calcolo nelle aree di lavoro di Machine Learning Services |
> | Azione | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Elenca i segreti per le risorse di calcolo nell'area di lavoro di Machine Learning Services |
> | Azione | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Elenca i nodi per le risorse di calcolo nell'area di lavoro di Machine Learning Services |
> | Azione | Microsoft.MachineLearningServices/workspaces/computes/read | Recupera le risorse di calcolo nelle aree di lavoro di Machine Learning Services |
> | Azione | Microsoft.MachineLearningServices/workspaces/computes/write | Crea o aggiorna le risorse di calcolo nelle aree di lavoro di Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datadriftdetectors/Read | Recupera i rilevatori di derivazione dei dati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datadriftdetectors/Write | Crea o aggiorna i rilevatori della deriva dati in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registered/Delete | Elimina i set di impostazioni registrati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrato/anteprima/lettura | Ottiene l'anteprima del DataSet per i set di dati registrati in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrato/profilo/lettura | Ottiene i profili del set di dati per i set di dati registrati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrato/profilo/scrittura | Crea o aggiorna i profili dei set di dati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registered/Read | Ottiene i set di impostazioni registrati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registered/Write | Crea o aggiorna i set di impostazioni registrati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/non registrato/Delete | Elimina i set di impostazioni non registrati in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/non registrato/anteprima/lettura | Ottiene l'anteprima del set di dati per i set di dati non registrati in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/non registrato/profilo/lettura | Ottiene i profili del set di dati per i set di dati non registrati in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/non registrato/Read | Ottiene i set di impostazioni non registrati in Machine Learning Services area di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/unregistered/Write | Crea o aggiorna i set di impostazioni non registrati in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datastores/Delete | Elimina gli archivi dati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datastores/Read | Recupera gli archivi dati nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datastores/Write | Crea o aggiorna gli archivi dati nelle aree di lavoro Machine Learning Services |
> | Azione | Microsoft.MachineLearningServices/workspaces/delete | Elimina le aree di lavoro di Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Endpoints/Pipelines/Read | Ottiene pipeline pubblicate ed endpoint della pipeline in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Endpoints/Pipelines/Write | Crea o aggiorna le pipeline pubblicate e gli endpoint della pipeline in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/environments/Build/Action | Compila gli ambienti in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/environments/Read | Recupera gli ambienti nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/environments/readSecrets/Action | Recupera gli ambienti con i segreti in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/environments/Write | Crea o aggiorna gli ambienti in Machine Learning Services aree di lavoro |
> | Azione | Microsoft. MachineLearningServices/Workspaces/eventGridFilters/Read | Ottenere un filtro di griglia di eventi per una determinata area di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Experiments/Delete | Elimina gli esperimenti nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Experiments/Read | Recupera gli esperimenti nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Experiments/Run/Read | Ottiene le esecuzioni in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Experiments/Run/scriptRun/Submit/Action | Crea o aggiorna le esecuzioni di script in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Experiments/Run/Write | Crea o aggiorna esecuzioni in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Crea o aggiorna gli esperimenti in Machine Learning Services aree di lavoro |
> | Azione | Microsoft.MachineLearningServices/workspaces/listKeys/action | Elenca i segreti per un'area di lavoro di Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Metadata/artefatts/Delete | Elimina gli artefatti nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Metadata/artefatts/Read | Ottiene gli artefatti nelle aree di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Metadata/artefatts/Write | Crea o aggiorna elementi in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Metadata/Snapshots/Delete | Elimina gli snapshot in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Metadata/Snapshots/Read | Ottiene gli snapshot in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Metadata/Snapshots/Write | Crea o aggiorna gli snapshot in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Models/Delete | Elimina i modelli nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/aree di lavoro/modelli/download/azione | Scarica i modelli nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Models/Package/Action | Pacchetti di modelli in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/aree di lavoro/modelli/lettura | Ottiene i modelli nell'area di lavoro Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/aree di lavoro/modelli/scrittura | Crea o aggiorna i modelli in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Modules/Read | Recupera i moduli in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Modules/Write | Crea o aggiorna il modulo in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Delete | Elimina le bozze di pipeline in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Read | Ottiene le bozze della pipeline in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Write | Crea o aggiorna le bozze di pipeline in Machine Learning Services aree di lavoro |
> | Azione | Microsoft.MachineLearningServices/workspaces/read | Recupera le aree di lavoro di Machine Learning Services |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/ACI/Delete | Elimina i servizi ACI in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/ACI/listkeys/Action | Elenca le chiavi per i servizi ACI in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/ACI/Write | Crea o aggiorna i servizi ACI in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/devtest/Delete | Elimina i servizi devtest AKS in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/devtest/listkeys/Action | Elenca le chiavi per i servizi AKS devtest in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/devtest/Score/Action | Punteggi devtest Servizi AKS in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/devtest/Write | Crea o aggiorna i servizi AKS devtest in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/prod/Delete | Elimina i servizi AKS di produzione in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/prod/listkeys/Action | Elenca le chiavi per i servizi AKS di produzione in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/prod/Score/Action | Classifica i servizi AKS in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKS/prod/Write | Crea o aggiorna i servizi AKS in Machine Learning Services aree di lavoro |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/Read | Ottiene i servizi in Machine Learning Services aree di lavoro |
> | Azione | Microsoft.MachineLearningServices/workspaces/write | Crea o aggiorna le aree di lavoro di Machine Learning Services |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. ManagedIdentity/Identities/Read | Ottiene un'identità assegnata di sistema esistente |
> | Azione | Microsoft. ManagedIdentity/Operations/Read | Elenca le operazioni disponibili nel provider di risorse Microsoft. ManagedIdentity |
> | Azione | Microsoft.ManagedIdentity/register/action | Registra la sottoscrizione per il provider di risorse di identità gestite |
> | Azione | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Azione di controllo degli accessi in base al ruolo per l'assegnazione dell'identità assegnata di un utente esistente a una risorsa |
> | Azione | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina l'identità assegnata a un utente esistente |
> | Azione | Microsoft.ManagedIdentity/userAssignedIdentities/read | Ottiene l'identità assegnata a un utente esistente |
> | Azione | Microsoft.ManagedIdentity/userAssignedIdentities/write | Crea una nuova identità assegnata a un utente esistente o aggiorna i tag associati a un'identità assegnata a un utente esistente |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/Read | Recupera un elenco di definizioni di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/Operations/Read | Recupera un elenco di operazioni dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/operationStatuses/Read | Legge lo stato dell'operazione per la risorsa. |
> | Azione | Microsoft. ManagedServices/Register/Action | Eseguire la registrazione ai servizi gestiti. |
> | Azione | Microsoft. ManagedServices/registrationAssignments/Delete | Rimuove l'assegnazione di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/registrationAssignments/Read | Recupera un elenco di assegnazioni di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/registrationAssignments/Write | Aggiungere o modificare l'assegnazione di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/registrationDefinitions/Delete | Rimuove la definizione di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/registrationDefinitions/Read | Recupera un elenco di definizioni di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/registrationDefinitions/Write | Aggiungere o modificare la definizione di registrazione dei servizi gestiti. |
> | Azione | Microsoft. ManagedServices/Annulla registrazione/azione | Annullare la registrazione da servizi gestiti. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Management/checkNameAvailability/action | Controlla se il nome del gruppo di gestione specificato è valido e univoco. |
> | Azione | Microsoft.Management/getEntities/action | Elenca tutte le entità, ovvero gruppi di gestione, sottoscrizioni e così via, per l'utente autenticato. |
> | Azione | Microsoft.Management/managementGroups/delete | Elimina un gruppo di gestione. |
> | Azione | Microsoft. Management/managementGroups/Descendants/Read | Ottiene tutti i discendenti (Gruppi di gestione, sottoscrizioni) di un gruppo di gestione. |
> | Azione | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | Azione | Microsoft.Management/managementGroups/subscriptions/delete | Annulla l'associazione della sottoscrizione dal gruppo di gestione. |
> | Azione | Microsoft.Management/managementGroups/subscriptions/write | Associa una sottoscrizione esistente al gruppo di gestione. |
> | Azione | Microsoft.Management/managementGroups/write | Crea o aggiorna un gruppo di gestione. |
> | Azione | Microsoft.Management/register/action | Registra la sottoscrizione specificata con Microsoft.Management. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede l'accesso in lettura ai dati in un account di Mappe. |
> | Azione | Microsoft.Maps/accounts/delete | Elimina un account Mappe |
> | Azione | Microsoft.Maps/accounts/eventGridFilters/delete | Elimina un filtro di Griglia di eventi |
> | Azione | Microsoft.Maps/accounts/eventGridFilters/read | Recupera un filtro di Griglia di eventi |
> | Azione | Microsoft.Maps/accounts/eventGridFilters/write | Crea o aggiorna un filtro di Griglia di eventi |
> | Azione | Microsoft.Maps/accounts/listKeys/action | Elenca le chiavi degli account Mappe |
> | Azione | Microsoft.Maps/accounts/read | Recupera un account Mappe |
> | Azione | Microsoft.Maps/accounts/regenerateKey/action | Genera una nuova chiave primaria o secondaria dell'account Mappe |
> | Azione | Microsoft.Maps/accounts/write | Crea o aggiorna un account Mappe |
> | Azione | Microsoft.Maps/operations/read | Leggere le operazioni del provider |
> | Azione | Microsoft.Maps/register/action | Registra il provider |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Restituisce un contratto. |
> | Azione | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accetta un contratto firmato. |
> | Azione | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa un'immagine nell'istanza di Registro Azure Container dell'utente finale. |
> | Azione | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Restituisce una configurazione. |
> | Azione | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Salva una configurazione. |
> | Azione | Microsoft.Marketplace/register/action | Registra il provider di risorse Microsoft.Marketplace nella sottoscrizione. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.MarketplaceApps/ClassicDevServices/delete | Esegue un'operazione DELETE su una risorsa ClassicDevService. |
> | Azione | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Ottiene le chiavi di gestione di una risorsa ClassicDevService. |
> | Azione | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Ottiene l'URL di Single Sign-On per un ClassicDevService. |
> | Azione | Microsoft.MarketplaceApps/ClassicDevServices/read | Esegue un'operazione GET su un ClassicDevService. |
> | Azione | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Genera le chiavi di gestione per una risorsa ClassicDevService. |
> | Azione | Microsoft.MarketplaceApps/Operations/read | Legge le operazioni per tutti i tipi di risorsa. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Elimina un contratto per uno specifico elemento del marketplace. |
> | Azione | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Restituisce un contratto per uno specifico elemento del marketplace. |
> | Azione | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Firma un contratto per uno specifico elemento del marketplace. |
> | Azione | Microsoft.MarketplaceOrdering/agreements/read | Restituisce tutti i contratti della sottoscrizione specificata |
> | Azione | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Restituisce un contratto per uno specifico elemento di macchina virtuale del marketplace |
> | Azione | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Firma o annulla un contratto per uno specifico elemento di macchina virtuale del marketplace |
> | Azione | Microsoft.MarketplaceOrdering/operations/read | Elencare tutte le operazioni possibili nell'API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Media/checknameavailability/action | Controlla se un nome di account di Servizi multimediali è disponibile |
> | Azione | Microsoft.Media/mediaservices/accountfilters/delete | Elimina qualsiasi filtro di account |
> | Azione | Microsoft.Media/mediaservices/accountfilters/read | Legge qualsiasi filtro di account |
> | Azione | Microsoft.Media/mediaservices/accountfilters/write | Crea o aggiorna qualsiasi filtro di account |
> | Azione | Microsoft.Media/mediaservices/assets/assetfilters/delete | Elimina eventuali filtri di asset |
> | Azione | Microsoft.Media/mediaservices/assets/assetfilters/read | Legge eventuali filtri di asset |
> | Azione | Microsoft.Media/mediaservices/assets/assetfilters/write | Crea o aggiorna eventuali filtri di asset |
> | Azione | Microsoft.Media/mediaservices/assets/delete | Elimina tutti gli asset |
> | Azione | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Recupera la chiave di crittografia dell'asset |
> | Azione | Microsoft.Media/mediaservices/assets/listContainerSas/action | Elenca gli URL di firma di accesso condiviso del contenitore di asset |
> | Azione | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Elenca i localizzatori di streaming per gli asset |
> | Azione | Microsoft.Media/mediaservices/assets/read | Legge tutti gli asset |
> | Azione | Microsoft.Media/mediaservices/assets/write | Crea o aggiorna tutti gli asset |
> | Azione | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Elimina tutti i criteri di chiave simmetrica |
> | Azione | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Recupera le proprietà dei criteri con i segreti |
> | Azione | Microsoft.Media/mediaservices/contentKeyPolicies/read | Legge tutti i criteri di chiave simmetrica |
> | Azione | Microsoft.Media/mediaservices/contentKeyPolicies/write | Crea o aggiorna tutti i criteri di chiave simmetrica |
> | Azione | Microsoft.Media/mediaservices/delete | Elimina tutti gli account di Servizi multimediali |
> | Azione | Microsoft.Media/mediaservices/eventGridFilters/delete | Elimina tutti i filtri di Griglia di eventi |
> | Azione | Microsoft.Media/mediaservices/eventGridFilters/read | Legge tutti i filtri di Griglia di eventi |
> | Azione | Microsoft.Media/mediaservices/eventGridFilters/write | Crea o aggiorna tutti i filtri di Griglia di eventi |
> | Azione | Microsoft.Media/mediaservices/liveEventOperations/read | Legge tutte le operazioni di eventi live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/delete | Elimina tutti gli eventi live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Elimina tutti gli output live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Legge tutti gli output live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Crea o aggiorna tutti gli output live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/read | Legge tutti gli eventi live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/reset/action | Reimposta tutte le operazioni di eventi live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/start/action | Avvia tutte le operazioni di eventi live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/stop/action | Arresta tutte le operazioni di eventi live |
> | Azione | Microsoft.Media/mediaservices/liveEvents/write | Crea o aggiorna tutti gli eventi live |
> | Azione | Microsoft.Media/mediaservices/liveOutputOperations/read | Legge tutte le operazioni di output live |
> | Azione | Microsoft.Media/mediaservices/read | Legge tutti gli account di Servizi multimediali |
> | Azione | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Legge tutte le operazioni degli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingEndpoints/delete | Elimina tutti gli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingEndpoints/read | Legge tutti gli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Ridimensiona tutte le operazioni degli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Avvia tutte le operazioni degli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Arresta tutte le operazioni degli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingEndpoints/write | Crea o aggiorna tutti gli endpoint di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingLocators/delete | Elimina tutti i localizzatori di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Elenca le chiavi simmetriche |
> | Azione | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Elenca i percorsi |
> | Azione | Microsoft.Media/mediaservices/streamingLocators/read | Legge tutti i localizzatori di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingLocators/write | Crea o aggiorna tutti i localizzatori di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingPolicies/delete | Elimina tutti i criteri di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingPolicies/read | Legge tutti i criteri di streaming |
> | Azione | Microsoft.Media/mediaservices/streamingPolicies/write | Crea o aggiorna tutti i criteri di streaming |
> | Azione | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizza le chiavi di archiviazione per un account di Archiviazione di Azure collegato |
> | Azione | Microsoft.Media/mediaservices/transforms/delete | Elimina tutte le trasformazioni |
> | Azione | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Annulla processo |
> | Azione | Microsoft.Media/mediaservices/transforms/jobs/delete | Elimina tutti i processi |
> | Azione | Microsoft.Media/mediaservices/transforms/jobs/read | Legge tutti i processi |
> | Azione | Microsoft.Media/mediaservices/transforms/jobs/write | Crea o aggiorna tutti i processi |
> | Azione | Microsoft.Media/mediaservices/transforms/read | Legge tutte le trasformazioni |
> | Azione | Microsoft.Media/mediaservices/transforms/write | Crea o aggiorna tutte le trasformazioni |
> | Azione | Microsoft.Media/mediaservices/write | Crea o aggiorna gli account di Servizi multimediali |
> | Azione | Microsoft.Media/operations/read | Ottiene le operazioni disponibili |
> | Azione | Microsoft.Media/register/action | Registra la sottoscrizione per il provider di risorse di Servizi multimediali e consente la creazione di account di Servizi multimediali |
> | Azione | Microsoft.Media/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse di Servizi multimediali |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. migrate/assessmentprojects/assessmentOptions/Read | Ottiene le opzioni di valutazione disponibili nella località specificata |
> | Azione | Microsoft.Migrate/assessmentprojects/assessments/read | Elenca le valutazioni in un progetto |
> | Azione | Microsoft.Migrate/assessmentprojects/delete | Elimina il progetto di valutazione |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Ottiene le proprietà di una macchina virtuale valutata |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Elimina una valutazione |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Scarica l'URL di un report di valutazione |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Ottiene le proprietà di una valutazione |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Crea una nuova valutazione o ne aggiorna una esistente |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/delete | Elimina un gruppo |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/read | Ottiene le proprietà di un gruppo |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Aggiornamento del gruppo mediante l'aggiunta o la rimozione di computer |
> | Azione | Microsoft.Migrate/assessmentprojects/groups/write | Crea un nuovo gruppo o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Elimina l'agente di raccolta HyperV |
> | Azione | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Ottiene le proprietà dell'agente di raccolta HyperV |
> | Azione | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Crea un nuovo agente di raccolta HyperV o aggiorna un agente di raccolta HyperV esistente |
> | Azione | Microsoft. migrate/assessmentprojects/importcollectors/Delete | Elimina l'agente di raccolta importazione |
> | Azione | Microsoft. migrate/assessmentprojects/importcollectors/Read | Ottiene le proprietà dell'agente di raccolta di importazione |
> | Azione | Microsoft. migrate/assessmentprojects/importcollectors/Write | Crea un nuovo agente di raccolta delle importazioni o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/assessmentprojects/machines/read | Ottiene le proprietà di una macchina virtuale |
> | Azione | Microsoft.Migrate/assessmentprojects/read | Ottiene le proprietà del progetto di valutazione |
> | Azione | Microsoft. migrate/assessmentprojects/servercollectors/Read | Ottiene le proprietà dell'agente di raccolta server |
> | Azione | Microsoft. migrate/assessmentprojects/servercollectors/Write | Crea un nuovo agente di raccolta server o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Elimina l'agente di raccolta VMware |
> | Azione | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Ottiene le proprietà dell'agente di raccolta VMware |
> | Azione | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Crea un nuovo agente di raccolta VMware o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/assessmentprojects/write | Crea un nuovo progetto di valutazione o aggiorna un progetto di valutazione esistente |
> | Azione | Microsoft.Migrate/locations/assessmentOptions/read | Ottiene le opzioni di valutazione disponibili nella località specificata |
> | Azione | Microsoft.Migrate/locations/checknameavailability/action | Verifica la disponibilità del nome della risorsa per la sottoscrizione specificata nella località specificata |
> | Azione | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Ottiene le proprietà di un'istanza di database |
> | Azione | Microsoft.Migrate/migrateprojects/Databases/read | Ottiene le proprietà di un database |
> | Azione | Microsoft. migrate/migrateprojects/Delete | Elimina un progetto di migrazione |
> | Azione | Microsoft.Migrate/migrateprojects/machines/read | Ottiene le proprietà di una macchina virtuale |
> | Azione | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Elimina un evento di migrazione |
> | Azione | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Ottiene le proprietà di un evento di migrazione. |
> | Azione | Microsoft.Migrate/migrateprojects/read | Ottiene le proprietà del progetto migrate |
> | Azione | Microsoft. migrate/migrateprojects/RefreshSummary/Action | Aggiorna il riepilogo del progetto di migrazione |
> | Azione | Microsoft.Migrate/migrateprojects/registerTool/action | Consente di registrare lo strumento in un progetto di migrazione |
> | Azione | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Pulire i dati della soluzione di migrazione del progetto |
> | Azione | Microsoft.Migrate/migrateprojects/solutions/Delete | Elimina una soluzione di migrazione del progetto |
> | Azione | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Ottiene la configurazione della soluzione del progetto di migrazione. |
> | Azione | Microsoft.Migrate/migrateprojects/solutions/read | Ottiene le proprietà della soluzione migrate Project |
> | Azione | Microsoft.Migrate/migrateprojects/solutions/write | Crea una nuova soluzione di progetto di migrazione o aggiorna una soluzione di progetto di migrazione esistente |
> | Azione | Microsoft.Migrate/migrateprojects/write | Crea un nuovo progetto di migrazione o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/Operations/read | Elenca le operazioni disponibili nel provider di risorse Microsoft.Migrate |
> | Azione | Microsoft.Migrate/projects/assessments/read | Elenca le valutazioni in un progetto |
> | Azione | Microsoft.Migrate/projects/delete | Elimina il progetto |
> | Azione | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Ottiene le proprietà di una macchina virtuale valutata |
> | Azione | Microsoft.Migrate/projects/groups/assessments/delete | Elimina una valutazione |
> | Azione | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Scarica l'URL di un report di valutazione |
> | Azione | Microsoft.Migrate/projects/groups/assessments/read | Ottiene le proprietà di una valutazione |
> | Azione | Microsoft.Migrate/projects/groups/assessments/write | Crea una nuova valutazione o ne aggiorna una esistente |
> | Azione | Microsoft.Migrate/projects/groups/delete | Elimina un gruppo |
> | Azione | Microsoft.Migrate/projects/groups/read | Ottiene le proprietà di un gruppo |
> | Azione | Microsoft.Migrate/projects/groups/read | Crea un nuovo gruppo o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/projects/keys/action | Ottiene le chiavi condivise per il progetto |
> | Azione | Microsoft.Migrate/projects/machines/read | Ottiene le proprietà di una macchina virtuale |
> | Azione | Microsoft.Migrate/projects/read | Ottiene le proprietà di un progetto |
> | Azione | Microsoft.Migrate/projects/write | Crea un nuovo progetto o ne aggiorna uno esistente |
> | Azione | Microsoft.Migrate/register/action | Registra la sottoscrizione con il provider di risorse Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.MixedReality/register/action | Registra una sottoscrizione per il provider di risorse di realtà mista. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Creare ancoraggi spaziali |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Elimina ancoraggi spaziali |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi spaziali vicini |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottenere le proprietà degli ancoraggi spaziali |
> | Azione | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene l'impostazione di diagnostica per Microsoft. MixedReality/spatialAnchorsAccounts |
> | Azione | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/diagnosticSettings/Write | Crea o aggiorna l'impostazione di diagnostica per Microsoft. MixedReality/spatialAnchorsAccounts |
> | Azione | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. Insights/metricDefinitions/Read | Ottiene le metriche disponibili per Microsoft. MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare ancoraggi spaziali |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio ancoraggi spaziali di Azure |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aggiornare le proprietà degli ancoraggi spaziali |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. NetApp/locations/checkfilepathavailability/Action | Controllare se il percorso del file è disponibile |
> | Azione | Microsoft. NetApp/locations/checknameavailability/Action | Verificare se il nome della risorsa è disponibile |
> | Azione | Microsoft.NetApp/locations/operationresults/read | Legge una risorsa di risultato di un'operazione. |
> | Azione | Microsoft.NetApp/locations/read | Legge una risorsa di controllo di disponibilità. |
> | Azione | Microsoft. NetApp/netAppAccounts/backupPolicies/Delete | Elimina una risorsa di criteri di backup. |
> | Azione | Microsoft. NetApp/netAppAccounts/backupPolicies/Read | Legge una risorsa di criteri di backup. |
> | Azione | Microsoft. NetApp/netAppAccounts/backupPolicies/Write | Scrive una risorsa di criteri di backup. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Elimina una risorsa di pool. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/read | Legge una risorsa di pool. |
> | Azione | Microsoft. NetApp/netAppAccounts/capacityPools/volumi/backup/Delete | Elimina una risorsa di backup. |
> | Azione | Microsoft. NetApp/netAppAccounts/capacityPools/volumi/backup/lettura | Legge una risorsa di backup. |
> | Azione | Microsoft. NetApp/netAppAccounts/capacityPools/volumi/backup/scrittura | Scrive una risorsa di backup. |
> | Azione | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/break/action | Interrompi relazioni di replica volume |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Elimina una risorsa di volume. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Legge una risorsa di destinazione di montaggio. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Legge una risorsa di volume. |
> | Azione | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/ReplicationStatus/Action | Legge gli Stati della replica del volume. |
> | Azione | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/Revert/Action | Ripristinare un volume allo snapshot specifico |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Elimina una risorsa di snapshot. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Legge una risorsa di snapshot. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Scrive una risorsa di snapshot. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Scrive una risorsa di volume. |
> | Azione | Microsoft.NetApp/netAppAccounts/capacityPools/write | Scrive una risorsa di pool. |
> | Azione | Microsoft.NetApp/netAppAccounts/delete | Elimina una risorsa account. |
> | Azione | Microsoft.NetApp/netAppAccounts/read | Legge una risorsa di account. |
> | Azione | Microsoft. NetApp/netAppAccounts/Vaults/Read | Legge una risorsa dell'insieme di credenziali. |
> | Azione | Microsoft.NetApp/netAppAccounts/write | Scrive una risorsa di account. |
> | Azione | Microsoft.NetApp/Operations/read | Legge una risorsa di operazione. |
> | Azione | Microsoft. NetApp/Register/Action | Registra la sottoscrizione con il provider di risorse Microsoft. NetApp |
> | Azione | Microsoft. NetApp/Unregister/Action | Annulla la registrazione della sottoscrizione con il provider di risorse Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Ottiene le intestazioni di richiesta disponibili del gateway applicazione |
> | Azione | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Ottiene le intestazioni di risposta disponibili del gateway applicazione |
> | Azione | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Ottiene le variabili del server disponibili del gateway applicazione |
> | Azione | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Criteri SSL predefiniti del gateway applicazione |
> | Azione | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opzioni SSL disponibili per il gateway applicazione |
> | Azione | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Ottiene i set di regole WAF disponibili sul gateway applicazione |
> | Azione | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Aggiunge un pool di indirizzi back-end del gateway applicazione. Non avvisabile. |
> | Azione | Microsoft.Network/applicationGateways/backendhealth/action | Ottiene l'integrità back-end del gateway applicazione |
> | Azione | Microsoft.Network/applicationGateways/delete | Elimina un gateway applicazione |
> | Azione | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Ottiene l'integrità back-end del gateway applicazione su richiesta per l'impostazione http e il pool back-end specificati |
> | Azione | Microsoft.Network/applicationGateways/read | Ottiene un gateway applicazione |
> | Azione | Microsoft.Network/applicationGateways/start/action | Avvia un gateway applicazione |
> | Azione | Microsoft.Network/applicationGateways/stop/action | Arresta un gateway applicazione |
> | Azione | Microsoft.Network/applicationGateways/write | Crea un gateway applicazione o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Elimina un criterio WAF del gateway applicazione |
> | Azione | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Ottiene un criterio WAF del gateway applicazione |
> | Azione | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Crea un criterio WAF del gateway applicazione o aggiorna un criterio WAF del gateway applicazione |
> | Azione | Microsoft.Network/applicationSecurityGroups/delete | Elimina un gruppo di sicurezza dell'applicazione |
> | Azione | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Aggiunge una configurazione IP a gruppi di sicurezza dell'applicazione. Non avvisabile. |
> | Azione | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Aggiunge una regola di sicurezza a gruppi di sicurezza dell'applicazione. Non avvisabile. |
> | Azione | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Elenca le configurazioni IP in ApplicationSecurityGroup |
> | Azione | Microsoft.Network/applicationSecurityGroups/read | Ottiene un ID gruppo di sicurezza dell'applicazione. |
> | Azione | Microsoft.Network/applicationSecurityGroups/write | Crea un gruppo di sicurezza dell'applicazione o aggiorna un gruppo di sicurezza dell'applicazione esistente. |
> | Azione | Microsoft.Network/azureFirewallFqdnTags/read | Ottiene i tag FQDN del Firewall di Azure |
> | Azione | Microsoft.Network/azurefirewalls/delete | Elimina Firewall di Azure |
> | Azione | Microsoft.Network/azurefirewalls/read | Ottiene Firewall di Azure |
> | Azione | Microsoft.Network/azurefirewalls/write | Crea o aggiorna un'istanza di Firewall di Azure. |
> | Azione | Microsoft. Network/bastionHosts/createbsl/Action | Crea URL condivisibili per le macchine virtuali in un Bastion e restituisce gli URL |
> | Azione | Microsoft.Network/bastionHosts/delete | Elimina un Bastion Host |
> | Azione | Microsoft. Network/bastionHosts/deletebsl/Action | Elimina gli URL condivisibili per le VM fornite in un Bastion |
> | Azione | Microsoft. Network/bastionHosts/disconnectactivesessions/Action | Disconnettere le sessioni attive specificate nell'host Bastion |
> | Azione | Microsoft. Network/bastionHosts/getactivesessions/Action | Ottenere sessioni attive nell'host Bastion |
> | Azione | Microsoft. Network/bastionHosts/getbsl/Action | Restituisce gli URL condivisibili per le macchine virtuali specificate in una subnet Bastion, purché vengano creati gli URL |
> | Azione | Microsoft.Network/bastionHosts/read | Recupera un Bastion Host |
> | Azione | Microsoft.Network/bastionHosts/write | Crea o aggiorna un Bastion Host |
> | Azione | Microsoft.Network/bgpServiceCommunities/read | Ottiene le community del servizio BGP |
> | Azione | Microsoft.Network/checkFrontDoorNameAvailability/action | Verifica se è disponibile un nome di Frontdoor |
> | Azione | Microsoft.Network/checkTrafficManagerNameAvailability/action | Controlla la disponibilità di un nome DNS relativo di gestione traffico. |
> | Azione | Microsoft.Network/connections/delete | Elimina una connessione di gateway di rete virtuale |
> | Azione | Microsoft.Network/connections/read | Ottiene una connessione di gateway di rete virtuale |
> | Azione | Microsoft.Network/connections/revoke/action | Contrassegna lo stato di una connessione Express Route come revocato |
> | Azione | Microsoft.Network/connections/sharedkey/action | Ottiene la chiave condivisa della connessione gateway di rete virtuale |
> | Azione | Microsoft.Network/connections/sharedKey/read | Ottiene una connessione di gateway di rete virtuale SharedKey |
> | Azione | Microsoft.Network/connections/sharedKey/write | Crea una connessione del gateway di rete virtuale SharedKey o ne aggiorna una esistente |
> | Azione | Microsoft. Network/Connections/startpacketcapture/Action | Avvia un'acquisizione di pacchetti di connessione del gateway di rete virtuale. |
> | Azione | Microsoft. Network/Connections/stoppacketcapture/Action | Arresta un'acquisizione di pacchetti di connessione del gateway di rete virtuale. |
> | Azione | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Ottiene la configurazione del dispositivo VPN della connessione gateway di rete virtuale |
> | Azione | Microsoft.Network/connections/write | Crea una connessione del gateway di rete virtuale o ne aggiorna una esistente |
> | Azione | Microsoft.Network/ddosCustomPolicies/delete | Cancella un criterio personalizzato DDoS |
> | Azione | Microsoft.Network/ddosCustomPolicies/read | Recupera una definizione di criterio personalizzato DDoS |
> | Azione | Microsoft.Network/ddosCustomPolicies/write | Crea un criterio personalizzato DDoS o aggiorna un criterio personalizzato DDoS esistente |
> | Azione | Microsoft.Network/ddosProtectionPlans/delete | Elimina un piano di Protezione DDoS |
> | Azione | Microsoft.Network/ddosProtectionPlans/join/action | Aggiunge un piano di protezione DDoS. Non avvisabile. |
> | Azione | Microsoft.Network/ddosProtectionPlans/read | Ottiene un piano di Protezione DDoS |
> | Azione | Microsoft.Network/ddosProtectionPlans/write | Crea un piano di Protezione DDoS o aggiorna un piano di Protezione DDoS esistente  |
> | Azione | Microsoft.Network/dnsoperationresults/read | Ottiene i risultati di un'operazione DNS |
> | Azione | Microsoft.Network/dnsoperationstatuses/read | Ottiene lo stato di un’operazione DNS  |
> | Azione | Microsoft.Network/dnszones/A/delete | Rimuove il set di record di un determinato nome e tipo "A" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/A/read | Ottiene il set di record di tipo "A" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/A/write | Crea o aggiorna un set di record di tipo "A" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/AAAA/delete | Rimuove il set di record di un determinato nome e tipo "AAAA" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/AAAA/read | Ottiene il set di record di tipo "AAAA" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/AAAA/write | Crea o aggiorna un set di record di tipo "AAAA" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/all/read | Ottiene i set di record DNS tra i tipi |
> | Azione | Microsoft.Network/dnszones/CAA/delete | Rimuove il set di record di un determinato nome e tipo "CAA" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/CAA/read | Ottiene il set di record di tipo "CAA" in formato JSON. Il set di record contiene durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/CAA/write | Crea o aggiorna un set di record di tipo "CAA" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/CNAME/delete | Rimuove il set di record di un determinato nome e tipo "CNAME" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/CNAME/read | Ottiene il set di record di tipo "CNAME" in formato JSON. Il set di record contiene durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/CNAME/write | Crea o aggiorna un set di record di tipo "CNAME" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/delete | Elimina la zona DNS in formato Json. Le proprietà di zona includono tag, etag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Azione | Microsoft.Network/dnszones/MX/delete | Rimuove il set di record di un determinato nome e tipo "MX" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/MX/read | Ottiene il set di record di tipo "MX" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/MX/write | Crea o aggiorna un set di record di tipo "MX" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/NS/delete | Elimina i set di record DNS di tipo NS. |
> | Azione | Microsoft.Network/dnszones/NS/read | Ottiene i set di record DNS di tipo NS. |
> | Azione | Microsoft.Network/dnszones/NS/write | Crea o aggiorna i set di record DNS di tipo NS. |
> | Azione | Microsoft.Network/dnszones/PTR/delete | Rimuove il set di record di un determinato nome e tipo "PTR" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/PTR/read | Ottiene il set di record di tipo "PTR" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/PTR/write | Crea o aggiorna un set di record di tipo "PTR" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/read | Ottiene la zona DNS in formato Json. Le proprietà di zona includono tag, etag, numberOfRecordSets e maxNumberOfRecordSets. Si tenga presente che questo comando non recupera il set di record contenuti all'interno della zona. |
> | Azione | Microsoft.Network/dnszones/recordsets/read | Ottiene i set di record DNS tra i tipi |
> | Azione | Microsoft.Network/dnszones/SOA/read | Ottiene i set di record DNS di tipo SOA. |
> | Azione | Microsoft.Network/dnszones/SOA/write | Crea o aggiorna i set di record DNS di tipo SOA. |
> | Azione | Microsoft.Network/dnszones/SRV/delete | Rimuove il set di record di un determinato nome e tipo "SRV" da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/SRV/read | Ottiene il set di record di tipo "SRV" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/SRV/write | Crea o aggiorna il set di record di tipo SRV. |
> | Azione | Microsoft.Network/dnszones/TXT/delete | Rimuove il set di record di un determinato nome e tipo ‘TXT’ da una zona DNS. |
> | Azione | Microsoft.Network/dnszones/TXT/read | Ottiene il set di record di tipo ‘TXT’, in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/dnszones/TXT/write | Crea o aggiorna un set di record di tipo ‘TXT’ all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/dnszones/write | Crea o aggiorna una zona DNS in un gruppo di risorse.  Consente di aggiornare i tag in una risorsa di zona DNS. Si tenga presente che questo comando non può essere utilizzato per creare o aggiornare i set di record all'interno della zona. |
> | Azione | Microsoft.Network/expressRouteCircuits/authorizations/delete | Elimina un’autorizzazione di circuito Express Route |
> | Azione | Microsoft.Network/expressRouteCircuits/authorizations/read | Ottiene un’autorizzazione di ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/authorizations/write | Crea un'autorizzazione di circuito Express Route o ne aggiorna una esistente |
> | Azione | Microsoft.Network/expressRouteCircuits/delete | Elimina un ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/join/action | Aggiunge un circuito Express route. Non avvisabile. |
> | Azione | Microsoft. Network/expressRouteCircuits/peering/arpTables/lettura | Ottiene una ArpTable di peering di ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Elimina una connessione circuito ExpressRoute |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Ottiene una connessione circuito ExpressRoute |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Crea o aggiorna una risorsa connessione circuito ExpressRoute esistente |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/delete | Elimina il peering di ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Ottiene una connessione circuito Express Route peer |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/read | Ottiene il peering di ExpressRouteCircuit |
> | Azione | Microsoft. Network/expressRouteCircuits/peering/routeTables/lettura | Ottiene una RouteTable di peering di ExpressRouteCircuit |
> | Azione | Microsoft. Network/expressRouteCircuits/peering/routeTablesSummary/lettura | Ottiene il riepilogo di una RouteTable di peering di ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Ottiene lo Stat di peering di ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/peerings/write | Crea il peering di ExpressRouteCircuit o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/expressRouteCircuits/read | Ottiene un ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/stats/read | Ottiene uno Stat di ExpressRouteCircuit |
> | Azione | Microsoft.Network/expressRouteCircuits/write | Crea un ExpressRouteCircuit o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/expressRouteCrossConnections/join/action | Unisce una cross Connection di Express route. Non avvisabile. |
> | Azione | Microsoft. Network/expressRouteCrossConnections/peering/arpTables/lettura | Ottiene una tabella ARP di peering delle Cross Connection ExpressRoute |
> | Azione | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Elimina un peering di Cross Connection ExpressRoute |
> | Azione | Microsoft.Network/expressRouteCrossConnections/peerings/read | Ottiene un peering di Cross Connection ExpressRoute |
> | Azione | Microsoft. Network/expressRouteCrossConnections/peering/routeTables/lettura | Ottiene una tabella di route delle Cross Connection ExpressRoute |
> | Azione | Microsoft. Network/expressRouteCrossConnections/peering/routeTableSummary/lettura | Ottiene un riepilogo della tabella di routing delle Cross Connection ExpressRoute |
> | Azione | Microsoft.Network/expressRouteCrossConnections/peerings/write | Crea un peering di Cross Connection ExpressRoute o aggiorna un peering di Cross Connection ExpressRoute esistente |
> | Azione | Microsoft.Network/expressRouteCrossConnections/read | Ottiene una Cross Connection ExpressRoute |
> | Azione | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Elimina una connessione ExpressRoute |
> | Azione | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Ottiene una connessione ExpressRoute |
> | Azione | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Crea una connessione ExpressRoute o aggiorna una connessione ExpressRoute esistente |
> | Azione | Microsoft.Network/expressRouteGateways/join/action | Aggiunge un gateway Express route. Non avvisabile. |
> | Azione | Microsoft.Network/expressRouteGateways/read | Ottiene il gateway di ExpressRoute |
> | Azione | Microsoft.Network/expressRoutePorts/delete | Elimina ExpressRoutePorts |
> | Azione | Microsoft.Network/expressRoutePorts/join/action | Unisce le porte di Express route. Non avvisabile. |
> | Azione | Microsoft.Network/expressRoutePorts/links/read | Ottiene un collegamento a ExpressRoute |
> | Azione | Microsoft.Network/expressRoutePorts/read | Ottiene ExpressRoutePorts |
> | Azione | Microsoft.Network/expressRoutePorts/write | Crea o aggiorna ExpressRoutePorts |
> | Azione | Microsoft.Network/expressRoutePortsLocations/read | Ottiene i percorsi delle porte ExpressRoute |
> | Azione | Microsoft.Network/expressRouteServiceProviders/read | Ottiene i provider del servizio Express Route |
> | Azione | Microsoft. Network/firewallPolicies/Delete | Elimina un criterio firewall |
> | Azione | Microsoft. Network/firewallPolicies/join/azione | Aggiunge un criterio del firewall. Non avvisabile. |
> | Azione | Microsoft. Network/firewallPolicies/Read | Ottiene un criterio firewall |
> | Azione | Microsoft. Network/firewallPolicies/regole/Delete | Elimina un gruppo di regole dei criteri firewall |
> | Azione | Microsoft. Network/firewallPolicies/regole/Read | Ottiene un gruppo di regole dei criteri firewall |
> | Azione | Microsoft. Network/firewallPolicies/regole/Write | Crea un gruppo di regole dei criteri firewall o aggiorna un gruppo di regole dei criteri firewall esistente |
> | Azione | Microsoft. Network/firewallPolicies/Write | Crea un criterio firewall o aggiorna un criterio firewall esistente |
> | Azione | Microsoft.Network/frontDoors/backendPools/delete | Elimina un pool back-end |
> | Azione | Microsoft.Network/frontDoors/backendPools/read | Recupera un pool back-end |
> | Azione | Microsoft.Network/frontDoors/backendPools/write | Crea o aggiorna un pool back-end |
> | Azione | Microsoft.Network/frontDoors/delete | Elimina una Frontdoor |
> | Azione | Microsoft.Network/frontDoors/frontendEndpoints/delete | Elimina un endpoint front-end |
> | Azione | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Disabilita HTTPS in un endpoint front-end |
> | Azione | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Abilita HTTPS in un endpoint front-end |
> | Azione | Microsoft.Network/frontDoors/frontendEndpoints/read | Recupera un endpoint front-end |
> | Azione | Microsoft.Network/frontDoors/frontendEndpoints/write | Crea o aggiorna un endpoint front-end |
> | Azione | Microsoft.Network/frontDoors/healthProbeSettings/delete | Elimina le impostazioni del probe di integrità |
> | Azione | Microsoft.Network/frontDoors/healthProbeSettings/read | Recupera le impostazioni del probe di integrità |
> | Azione | Microsoft.Network/frontDoors/healthProbeSettings/write | Crea o aggiorna le impostazioni del probe di integrità |
> | Azione | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Crea o aggiorna le impostazioni di bilanciamento del carico |
> | Azione | Microsoft.Network/frontDoors/loadBalancingSettings/read | Recupera le impostazioni di bilanciamento del carico |
> | Azione | Microsoft.Network/frontDoors/loadBalancingSettings/write | Crea o aggiorna le impostazioni di bilanciamento del carico |
> | Azione | Microsoft.Network/frontDoors/purge/action | Ripulisce il contenuto memorizzato nella cache da una Frontdoor |
> | Azione | Microsoft.Network/frontDoors/read | Recupera una Frontdoor |
> | Azione | Microsoft.Network/frontDoors/routingRules/delete | Elimina una regola di gestione |
> | Azione | Microsoft.Network/frontDoors/routingRules/read | Recupera una regola di gestione |
> | Azione | Microsoft.Network/frontDoors/routingRules/write | Crea o aggiorna una regola di gestione |
> | Azione | Microsoft.Network/frontDoors/validateCustomDomain/action | Convalida un endpoint del front-end per una Frontdoor |
> | Azione | Microsoft.Network/frontDoors/write | Crea o aggiorna una Frontdoor |
> | Azione | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Ottiene i set di regole gestite del firewall applicazione Web |
> | Azione | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Elimina un criterio di firewall dell'applicazione Web |
> | Azione | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/join/azione | Aggiunge un criterio di Web Application Firewall. Non avvisabile. |
> | Azione | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Recupera un criterio di firewall dell'applicazione Web |
> | Azione | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Crea o aggiorna un criterio di firewall dell'applicazione Web |
> | Azione | Microsoft. Network/ipGroups/Delete | Elimina un IpGroup |
> | Azione | Microsoft. Network/ipGroups/join/azione | Aggiunge un IpGroup. Non avvisabile. |
> | Azione | Microsoft. Network/ipGroups/Read | Ottiene un IpGroup |
> | Azione | Microsoft. Network/ipGroups/Write | Crea un IpGroup o aggiorna un IpGroups esistente |
> | Azione | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Aggiunge un pool di indirizzi back-end del servizio di bilanciamento del carico. Non avvisabile. |
> | Azione | Microsoft.Network/loadBalancers/backendAddressPools/read | Ottiene una definizione del pool di indirizzi di back-end del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/delete | Elimina un servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Crea un join della configurazione IP del front-end del servizio di bilanciamento del carico. Non avvisabile. |
> | Azione | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Ottiene una definizione della configurazione IP del front-end del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Aggiunge un pool NAT in ingresso del servizio di bilanciamento del carico. Non avvisabile. |
> | Azione | Microsoft.Network/loadBalancers/inboundNatPools/read | Ottiene una definizione del pool NAT in entrata del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/inboundNatRules/delete | Elimina una regola NAT in entrata del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aggiunge una regola NAT in ingresso del servizio di bilanciamento del carico. Non avvisabile. |
> | Azione | Microsoft.Network/loadBalancers/inboundNatRules/read | Ottiene una definizione di regola NAT in entrata del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/inboundNatRules/write | Crea una regola NAT in entrata del servizio di bilanciamento del carico o aggiorna una regola NAT in entrata di bilanciamento del carico esistente |
> | Azione | Microsoft.Network/loadBalancers/loadBalancingRules/read | Ottiene una definizione di regola di bilanciamento del carico per il servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/networkInterfaces/read | Ottiene i riferimenti a tutte le interfacce di rete sotto a un servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/outboundRules/read | Ottiene una definizione della regola in uscita del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/probes/join/action | Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe. Non avvisabile. |
> | Azione | Microsoft.Network/loadBalancers/probes/read | Ottiene un probe del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/virtualMachines/read | Ottiene i riferimenti a tutte le macchine virtuali sotto a un servizio di bilanciamento del carico |
> | Azione | Microsoft.Network/loadBalancers/write | Crea un servizio di bilanciamento del carico o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/localnetworkgateways/delete | Elimina il LocalNetworkGateway |
> | Azione | Microsoft.Network/localnetworkgateways/read | Ottiene il LocalNetworkGateway |
> | Azione | Microsoft.Network/localnetworkgateways/write | Crea un LocalNetworkGateway o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Ottiene i servizi di collegamento privato approvati automaticamente |
> | Azione | Microsoft.Network/locations/availableDelegations/read | Recupera le deleghe disponibili |
> | Azione | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Ottiene le risorse degli endpoint privati disponibili |
> | Azione | Microsoft. Network/locations/availableServiceAliases/Read | Ottiene gli alias del servizio disponibili |
> | Azione | Microsoft.Network/locations/bareMetalTenants/action | Alloca o convalida un tenant bare metal |
> | Azione | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica il supporto della rete accelerata |
> | Azione | Microsoft.Network/locations/checkDnsNameAvailability/read | Controlla se l'etichetta DNS è disponibile nel percorso specificato |
> | Azione | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Controlla la visibilità del servizio di collegamento privato |
> | Azione | Microsoft.Network/locations/operationResults/read | Ottiene il risultato di un'operazione POST o DELETE asincrona |
> | Azione | Microsoft.Network/locations/operations/read | Ottiene una risorsa per l'operazione che rappresenta lo stato di un'operazione asincrona |
> | Azione | Microsoft.Network/locations/serviceTags/read | Ottenere i tag del servizio |
> | Azione | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Recupera le dimensioni di macchine virtuali supportate |
> | Azione | Microsoft.Network/locations/usages/read | Ottiene la metrica di utilizzo delle risorse |
> | Azione | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Ottiene un elenco dei servizi di endpoint di rete virtuale disponibili |
> | Azione | Microsoft.Network/networkIntentPolicies/delete | Elimina un criterio relativo alle finalità di rete |
> | Azione | Microsoft.Network/networkIntentPolicies/read | Ottiene una descrizione del criterio relativo alle finalità di rete |
> | Azione | Microsoft.Network/networkIntentPolicies/write | Crea un criterio con finalità di rete o aggiorna un criterio con finalità di rete esistente |
> | Azione | Microsoft.Network/networkInterfaces/delete | Elimina un'interfaccia di rete |
> | Azione | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Ottiene i gruppi di sicurezza di rete configurati sull’interfaccia di rete della macchina virtuale |
> | Azione | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Ottiene la tabella di instradamento configurata sull’interfaccia di rete della macchina virtuale |
> | Azione | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Crea un join di una configurazione IP dell'interfaccia di rete. Non avvisabile. |
> | Azione | Microsoft.Network/networkInterfaces/ipconfigurations/read | Ottiene la definizione della configurazione IP dell’interfaccia di rete.  |
> | Azione | Microsoft.Network/networkInterfaces/join/action | Aggiunge una macchina virtuale a un'interfaccia di rete. Non avvisabile. |
> | Azione | Microsoft.Network/networkInterfaces/loadBalancers/read | Ottiene tutti i servizi di bilanciamento del carico ai quali appartiene l’interfaccia di rete |
> | Azione | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Azione | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Elimina una configurazione TAP dell'interfaccia di rete. |
> | Azione | Microsoft.Network/networkInterfaces/tapConfigurations/read | Ottiene una configurazione TAP dell'interfaccia di rete. |
> | Azione | Microsoft.Network/networkInterfaces/tapConfigurations/write | Crea una configurazione TAP dell'interfaccia di rete o ne aggiorna una esistente. |
> | Azione | Microsoft.Network/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | Azione | Microsoft.Network/networkProfiles/delete | Elimina un profilo di rete |
> | Azione | Microsoft.Network/networkProfiles/read | Ottiene un profilo di rete |
> | Azione | Microsoft.Network/networkProfiles/removeContainers/action | Rimuove i contenitori |
> | Azione | Microsoft.Network/networkProfiles/setContainers/action | Imposta i contenitori |
> | Azione | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Imposta le interfacce di rete dei contenitori |
> | Azione | Microsoft.Network/networkProfiles/write | Crea o aggiorna un profilo di rete |
> | Azione | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Ottiene una definizione delle regole di sicurezza predefinita |
> | Azione | Microsoft.Network/networkSecurityGroups/delete | Elimina un gruppo di sicurezza di rete |
> | Azione | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non avvisabile. |
> | Azione | Microsoft.Network/networkSecurityGroups/read | Ottiene una definizione del gruppo di sicurezza di rete |
> | Azione | Microsoft.Network/networkSecurityGroups/securityRules/delete | Elimina una regola di sicurezza |
> | Azione | Microsoft.Network/networkSecurityGroups/securityRules/read | Ottiene una definizione delle regole di sicurezza |
> | Azione | Microsoft.Network/networkSecurityGroups/securityRules/write | Crea una regola di sicurezza o ne aggiorna una esistente |
> | Azione | Microsoft.Network/networkSecurityGroups/write | Crea un gruppo di sicurezza di rete o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/networkWatchers/availableProvidersList/action | Restituisce tutti i provider di servizi Internet disponibili per un'area di Azure specificata. |
> | Azione | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Restituisce il punteggio di latenza relativa per i provider di servizi Internet da una località specificata alle aree di Azure. |
> | Azione | Microsoft.Network/networkWatchers/configureFlowLog/action | Configura la registrazione del flusso per una risorsa di destinazione. |
> | Azione | Microsoft.Network/networkWatchers/connectionMonitors/delete | Elimina un monitoraggio connessione |
> | Azione | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Esegue query sul monitoraggio della connettività tra gli endpoint specificati |
> | Azione | Microsoft.Network/networkWatchers/connectionMonitors/read | Ottiene dettagli di monitoraggio connessione |
> | Azione | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Avvia il monitoraggio della connettività tra gli endpoint specificati |
> | Azione | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Arresta/mette in pausa il monitoraggio della connettività tra gli endpoint specificati |
> | Azione | Microsoft.Network/networkWatchers/connectionMonitors/write | Crea un monitoraggio connessione |
> | Azione | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifica se è possibile stabilire una connessione TCP diretta da una macchina virtuale a un determinato endpoint inclusa un'altra VM o un server remoto arbitrario. |
> | Azione | Microsoft.Network/networkWatchers/delete | Elimina un Network Watcher |
> | Azione | Microsoft. Network/networkWatchers/dashboard/Delete | Elimina un log di flusso |
> | Azione | Microsoft. Network/networkWatchers/dashboard/Read | Ottenere i dettagli del log di flusso |
> | Azione | Microsoft. Network/networkWatchers/dashboard/Write | Crea un log di flusso |
> | Azione | Microsoft.Network/networkWatchers/ipFlowVerify/action | Indica se il pacchetto è autorizzato o meno su o da una specifica destinazione. |
> | Azione | Microsoft.Network/networkWatchers/lenses/delete | Elimina una sezione |
> | Azione | Microsoft.Network/networkWatchers/lenses/query/action | Esegue query sul monitoraggio del traffico di rete in un endpoint specificato |
> | Azione | Microsoft.Network/networkWatchers/lenses/read | Ottiene i dettagli della sezione |
> | Azione | Microsoft.Network/networkWatchers/lenses/start/action | Avvia il monitoraggio del traffico di rete in un endpoint specificato |
> | Azione | Microsoft.Network/networkWatchers/lenses/stop/action | Arresta/mette in pausa il monitoraggio del traffico di rete in un endpoint specificato |
> | Azione | Microsoft.Network/networkWatchers/lenses/write | Crea una sezione |
> | Azione | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostica della configurazione di rete. |
> | Azione | Microsoft.Network/networkWatchers/nextHop/action | Per un indirizzo IP destinazione specificato, restituisce il tipo di hop successivo e l’indirizzo IP dell’hop successivo. |
> | Azione | Microsoft.Network/networkWatchers/packetCaptures/delete | Elimina un'acquisizione di pacchetti |
> | Azione | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Ottiene informazioni sulle proprietà e sullo stato di una risorsa di acquisizione dei pacchetti. |
> | Azione | Microsoft.Network/networkWatchers/packetCaptures/read | Ottiene la definizione di acquisizione dei pacchetti |
> | Azione | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Arresta la sessione di acquisizione dei pacchetti in corso. |
> | Azione | Microsoft.Network/networkWatchers/packetCaptures/write | Crea un'acquisizione di pacchetti |
> | Azione | Microsoft.Network/networkWatchers/pingMeshes/delete | Elimina un PingMesh |
> | Azione | Microsoft.Network/networkWatchers/pingMeshes/read | Ottiene i dettagli del PingMesh |
> | Azione | Microsoft.Network/networkWatchers/pingMeshes/start/action | Avvia un PingMesh tra macchine virtuali specificate |
> | Azione | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Arresta il PingMesh tra le macchine virtuali specificate |
> | Azione | Microsoft.Network/networkWatchers/pingMeshes/write | Crea un PingMesh |
> | Azione | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Esegue un batch di query sul monitoraggio della connettività tra gli endpoint specificati |
> | Azione | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Ottiene lo stato della registrazione del flusso su una risorsa. |
> | Azione | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Ottiene il risultato della risoluzione dei problemi dall’esecuzione precedente o dall’operazione di risoluzione dei problemi attualmente in corso. |
> | Azione | Microsoft.Network/networkWatchers/read | Ottiene la definizione del Network Watcher |
> | Azione | Microsoft.Network/networkWatchers/securityGroupView/action | Visualizza le regole del gruppo di sicurezza di rete configurate e in vigore applicate a una macchina virtuale. |
> | Azione | Microsoft.Network/networkWatchers/topology/action | Ottiene una vista a livello di rete delle risorse e le relative relazioni in un gruppo di risorse. |
> | Azione | Microsoft.Network/networkWatchers/troubleshoot/action | Avvia la risoluzione dei problemi su una risorsa di rete in Azure. |
> | Azione | Microsoft.Network/networkWatchers/write | Crea un Network Watcher o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/operations/read | Ottiene le operazioni disponibili |
> | Azione | Microsoft.Network/p2sVpnGateways/delete | Elimina un gateway VPN da punto a sito. |
> | Azione | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/Action | Disconnettere le connessioni VPN P2S |
> | Azione | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Genera il profilo VPN del gateway VPN da punto a sito |
> | Azione | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Recupera lo stato di integrità di una connessione VPN P2S per P2SVpnGateway |
> | Azione | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Ottiene l'integrità della connessione VPN P2S dettagliata per P2SVpnGateway |
> | Azione | Microsoft.Network/p2sVpnGateways/read | Ottiene un gateway VPN da punto a sito. |
> | Azione | Microsoft.Network/p2sVpnGateways/write | Inserisce un gateway VPN da punto a sito. |
> | Azione | Microsoft.Network/privateDnsOperationResults/read | Ottiene i risultati di un'operazione di DNS privato |
> | Azione | Microsoft.Network/privateDnsOperationStatuses/read | Ottiene lo stato di un'operazione di DNS privato |
> | Azione | Microsoft.Network/privateDnsZones/A/delete | Rimuovere il set di record di un determinato nome e digitare "A" da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/A/read | Ottenere il set di record di tipo "A" all'interno di una zona DNS privato in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/privateDnsZones/A/write | Crea o aggiorna un set di record di tipo "A" all'interno di una zona DNS privato. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/privateDnsZones/AAAA/delete | Rimuovere il set di record di un determinato nome e tipo "AAAA" da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/AAAA/read | Ottenere il set di record di tipo "AAAA" all'interno di una zona DNS privato in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/privateDnsZones/AAAA/write | Crea o aggiorna un set di record di tipo "AAAA" all'interno di una zona DNS privato. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/privateDnsZones/ALL/read | Ottiene DNS privato set di record tra i tipi |
> | Azione | Microsoft.Network/privateDnsZones/CNAME/delete | Rimuovere il set di record di un determinato nome e digitare "CNAME" da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/CNAME/read | Ottenere il set di record di tipo ' CNAME ' all'interno di una zona DNS privato in formato JSON. |
> | Azione | Microsoft.Network/privateDnsZones/CNAME/write | Crea o aggiorna un set di record di tipo ' CNAME ' all'interno di una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/delete | Eliminare una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/MX/delete | Rimuovere il set di record di un determinato nome e tipo "MX" da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/MX/read | Ottenere il set di record di tipo ' MX ' all'interno di una zona DNS privato in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/privateDnsZones/MX/write | Crea o aggiorna un set di record di tipo ' MX ' all'interno di una zona DNS privato. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/privateDnsZones/PTR/delete | Rimuovere il set di record di un determinato nome e tipo "PTR" da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/PTR/read | Ottenere il set di record di tipo ' PTR ' all'interno di una zona DNS privato in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/privateDnsZones/PTR/write | Crea o aggiorna un set di record di tipo "PTR" all'interno di una zona DNS privato. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/privateDnsZones/read | Ottenere le proprietà della zona DNS privato in formato JSON. Si noti che questo comando non consente di recuperare le reti virtuali a cui è collegata la zona di DNS privato o i set di record contenuti nella zona. |
> | Azione | Microsoft.Network/privateDnsZones/recordsets/read | Ottiene DNS privato set di record tra i tipi |
> | Azione | Microsoft.Network/privateDnsZones/SOA/read | Ottenere il set di record di tipo ' SOA ' all'interno di una zona DNS privato in formato JSON. |
> | Azione | Microsoft.Network/privateDnsZones/SOA/write | Aggiornare un set di record di tipo ' SOA ' all'interno di una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/SRV/delete | Rimuovere il set di record di un determinato nome e tipo "SRV" da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/SRV/read | Ottenere il set di record di tipo "SRV" all'interno di una zona DNS privato in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/privateDnsZones/SRV/write | Crea o aggiorna un set di record di tipo "SRV" all'interno di una zona DNS privato. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/privateDnsZones/TXT/delete | Rimuovere il set di record di un determinato nome e tipo ' TXT ' da una zona DNS privato. |
> | Azione | Microsoft.Network/privateDnsZones/TXT/read | Ottenere il set di record di tipo ' TXT ' all'interno di una zona DNS privato in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag. |
> | Azione | Microsoft.Network/privateDnsZones/TXT/write | Crea o aggiorna un set di record di tipo ' TXT ' all'interno di una zona DNS privato. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record. |
> | Azione | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Consente di eliminare un collegamento alla zona DNS privato alla rete virtuale. |
> | Azione | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Ottenere il collegamento della zona DNS privato alle proprietà della rete virtuale, in formato JSON. |
> | Azione | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Crea o aggiorna un collegamento di zona DNS privato alla rete virtuale. |
> | Azione | Microsoft.Network/privateDnsZones/write | Creare o aggiornare una zona DNS privato all'interno di un gruppo di risorse. Si noti che questo comando non può essere usato per creare o aggiornare i collegamenti di rete virtuale o i set di record all'interno della zona. |
> | Azione | Microsoft. Network/privateEndpointRedirectMaps/Read | Ottiene un endpoint privato RedirectMap |
> | Azione | Microsoft. Network/privateEndpointRedirectMaps/Write | Crea un endpoint privato RedirectMap o aggiorna un endpoint privato esistente RedirectMap |
> | Azione | Microsoft.Network/privateEndpoints/delete | Elimina una risorsa endpoint privata. |
> | Azione | Microsoft.Network/privateEndpoints/read | Ottiene una risorsa di endpoint privata. |
> | Azione | Microsoft.Network/privateEndpoints/write | Crea un nuovo endpoint privato o aggiorna un endpoint privato esistente. |
> | Azione | Microsoft.Network/privateLinkServices/delete | Elimina una risorsa servizio di collegamento privato. |
> | Azione | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Elimina una connessione all'endpoint privato. |
> | Azione | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Ottiene una definizione di connessione all'endpoint privato. |
> | Azione | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Crea una nuova connessione all'endpoint privato o aggiorna una connessione all'endpoint privato esistente. |
> | Azione | Microsoft.Network/privateLinkServices/read | Ottiene una risorsa servizio di collegamento privato. |
> | Azione | Microsoft.Network/privateLinkServices/write | Crea un nuovo servizio di collegamento privato o ne aggiorna uno esistente. |
> | Azione | Microsoft.Network/publicIPAddresses/delete | Elimina un indirizzo IP pubblico. |
> | Azione | Microsoft.Network/publicIPAddresses/join/action | Aggiunge un indirizzo IP pubblico. Non avvisabile. |
> | Azione | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Azione | Microsoft.Network/publicIPAddresses/write | Crea un indirizzo IP pubblico o ne aggiorna uno esistente.  |
> | Azione | Microsoft.Network/publicIPPrefixes/delete | Elimina un prefisso IP pubblico |
> | Azione | Microsoft.Network/publicIPPrefixes/join/action | Aggiunge un PublicIPPrefix. Non avvisabile. |
> | Azione | Microsoft.Network/publicIPPrefixes/read | Ottiene una definizione di prefisso IP pubblico |
> | Azione | Microsoft.Network/publicIPPrefixes/write | Crea un prefisso IP pubblico o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/register/action | Registra la sottoscrizione |
> | Azione | Microsoft.Network/routeFilters/delete | Elimina una definizione del filtro di instradamento |
> | Azione | Microsoft.Network/routeFilters/join/action | Aggiunge un filtro di route. Non avvisabile. |
> | Azione | Microsoft.Network/routeFilters/read | Ottiene una definizione del filtro di instradamento |
> | Azione | Microsoft.Network/routeFilters/routeFilterRules/delete | Elimina una definizione della regola del filtro di instradamento |
> | Azione | Microsoft.Network/routeFilters/routeFilterRules/read | Ottiene una definizione della regola del filtro di instradamento |
> | Azione | Microsoft.Network/routeFilters/routeFilterRules/write | Crea una regola del filtro di instradamento o ne aggiorna una esistente |
> | Azione | Microsoft.Network/routeFilters/write | Crea un filtro route o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/routeTables/delete | Elimina una definizione della tabella di route |
> | Azione | Microsoft.Network/routeTables/join/action | Aggiunge una tabella di route. Non avvisabile. |
> | Azione | Microsoft.Network/routeTables/read | Ottiene una definizione della tabella di route |
> | Azione | Microsoft.Network/routeTables/routes/delete | Elimina una definizione di route |
> | Azione | Microsoft.Network/routeTables/routes/read | Ottiene una definizione di route |
> | Azione | Microsoft.Network/routeTables/routes/write | Crea una route o ne aggiorna una esistente |
> | Azione | Microsoft.Network/routeTables/write | Crea una tabella di route o ne aggiorna una esistente |
> | Azione | Microsoft.Network/serviceEndpointPolicies/delete | Elimina un criterio di endpoint di servizio |
> | Azione | Microsoft.Network/serviceEndpointPolicies/join/action | Aggiunge un criterio di endpoint di servizio. Non avvisabile. |
> | Azione | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Aggiunge una subnet ai criteri dell'endpoint di servizio. Non avvisabile. |
> | Azione | Microsoft.Network/serviceEndpointPolicies/read | Ottiene una descrizione dei criteri di endpoint di servizio |
> | Azione | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Elimina una definizione di criteri di endpoint di servizio |
> | Azione | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Ottiene la descrizione di una definizione dei criteri di endpoint di servizio |
> | Azione | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Crea una definizione di criteri di endpoint di servizio o aggiorna una definizione di criteri di endpoint di servizio esistente |
> | Azione | Microsoft.Network/serviceEndpointPolicies/write | Crea criteri di endpoint di servizio o aggiorna criteri di endpoint di servizio esistenti |
> | Azione | Microsoft.Network/trafficManagerGeographicHierarchies/read | Ottiene la gerarchia geografica di Gestione traffico contenente le aree che possono essere utilizzate con il metodo di routing del traffico geografico |
> | Azione | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Elimina un Endpoint di Azure da un profilo di Gestione traffico esistente. Gestione traffico smetterà di eseguire il routing del traffico all'Endpoint di Azure eliminato. |
> | Azione | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Ottiene un Endpoint di Azure che appartiene a un profilo di Gestione traffico, incluse tutte le proprietà di tale Endpoint di Azure. |
> | Azione | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Aggiunge un nuovo Endpoint di Azure in un profilo di Gestione traffico esistente o aggiorna le proprietà di un Endpoint di Azure esistente in quel profilo di Gestione traffico. |
> | Azione | Microsoft.Network/trafficManagerProfiles/delete | Elimina il profilo di Gestione traffico. Tutte le impostazioni associate al profilo di Gestione traffico andranno perse e il profilo non può più essere utilizzato per instradare il traffico. |
> | Azione | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Elimina un endpoint esterno da un profilo di Gestione traffico esistente. Gestione traffico smetterà di eseguire il routing del traffico all'endpoint esterno eliminato. |
> | Azione | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Ottiene un endpoint esterno che appartiene a un profilo di Gestione traffico, incluse tutte le proprietà di tale endpoint esterno. |
> | Azione | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Aggiunge un nuovo endpoint esterno in un profilo di Gestione traffico esistente o aggiorna le proprietà di un endpoint esterno esistente in quel profilo di Gestione traffico. |
> | Azione | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Ottiene la mappa termica di Gestione traffico per il profilo di Gestione traffico specificato che contiene conteggi di query e dati di latenza per percorso e IP di origine. |
> | Azione | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Elimina un endpoint annidato da un profilo di Gestione traffico esistente. Gestione traffico smetterà di eseguire il routing del traffico all'endpoint annidato eliminato. |
> | Azione | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Ottiene un endpoint annidato che appartiene a un profilo di Gestione traffico, incluse tutte le proprietà di tale endpoint annidato. |
> | Azione | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Aggiunge un nuovo endpoint annidato in un profilo di Gestione traffico esistente o aggiorna le proprietà di un endpoint annidato esistente in quel profilo di Gestione traffico. |
> | Azione | Microsoft.Network/trafficManagerProfiles/read | Ottiene la configurazione del profilo di Gestione traffico. Include le impostazioni DNS, di routing del traffico, di monitoraggio dell'endpoint e un elenco degli endpoint instradati da questo profilo di Gestione traffico. |
> | Azione | Microsoft.Network/trafficManagerProfiles/write | Crea un profilo di Gestione traffico o modifica la configurazione di un profilo di Gestione traffico esistente.<br>Questo include l’abilitazione o la disabilitazione di un profilo e la modifica delle impostazioni di DNS, di instradamento del traffico o di monitoraggio dell’endpoint.<br>Gli endpoint instradati dal profilo di Gestione traffico possono essere aggiunti, rimossi, abilitati o disabilitati. |
> | Azione | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Elimina la chiave a livello di sottoscrizione utilizzata per la raccolta della metrica utente in tempo reale. |
> | Azione | Microsoft.Network/trafficManagerUserMetricsKeys/read | Ottiene la chiave a livello di sottoscrizione utilizzata per la raccolta della metrica utente in tempo reale. |
> | Azione | Microsoft.Network/trafficManagerUserMetricsKeys/write | Crea una nuova chiave a livello di sottoscrizione da usare per la raccolta della metrica utente in tempo reale. |
> | Azione | Microsoft.Network/unregister/action | Annulla la registrazione della sottoscrizione. |
> | Azione | Microsoft.Network/virtualHubs/delete | Elimina un hub virtuale |
> | Azione | Microsoft. Network/virtualHubs/effectiveRoutes/Action | Ottiene una route valida configurata nell'hub virtuale |
> | Azione | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Elimina una connessione di rete virtuale dell'hub |
> | Azione | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Ottiene una connessione di rete virtuale dell'hub |
> | Azione | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Crea o aggiorna una connessione di rete virtuale dell'hub |
> | Azione | Microsoft.Network/virtualHubs/read | Ottiene un hub virtuale |
> | Azione | Microsoft. Network/virtualHubs/routeTables/Delete | Eliminare un VirtualHubRouteTableV2 |
> | Azione | Microsoft. Network/virtualHubs/routeTables/Read | Ottenere un VirtualHubRouteTableV2 |
> | Azione | Microsoft. Network/virtualHubs/routeTables/Write | Crea o aggiorna un VirtualHubRouteTableV2 |
> | Azione | Microsoft.Network/virtualHubs/write | Crea o aggiorna un hub virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/connections/read | Ottiene una connessione di gateway di rete virtuale |
> | Azione | Microsoft.Network/virtualNetworkGateways/delete | Elimina un gateway di rete virtuale |
> | Azione | Microsoft. Network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/Action | Disconnettere le connessioni VPN del gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Genera il pacchetto VpnClient per il gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Genera il pacchetto VpnProfile per il gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Ottiene le route pubblicizzate per il gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Ottiene lo stato peer BGP pubblicizzato per il gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Ottiene le route apprese dal gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Ottiene lo stato di integrità della connessione del client VPN per il gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Ottiene i parametri IPsec client VPN per il client P2S del gateway di rete virtuale. |
> | Azione | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Ottiene l'URL di un pacchetto di profilo client VPN generato in precedenza |
> | Azione | Microsoft.Network/virtualNetworkGateways/read | Ottiene un gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/reset/action | Reimposta un gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reimposta la chiave condivisa del client VPN per il client P2S del gateway di rete virtuale |
> | Azione | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Imposta i parametri IPsec client VPN per il client P2S del gateway di rete virtuale. |
> | Azione | Microsoft. Network/virtualnetworkgateways/startpacketcapture/Action | Avvia un'acquisizione di pacchetti del gateway di rete virtuale. |
> | Azione | Microsoft. Network/virtualnetworkgateways/stoppacketcapture/Action | Arresta un'acquisizione di pacchetti del gateway di rete virtuale. |
> | Azione | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Elenca i dispositivi VPN supportati |
> | Azione | Microsoft.Network/virtualNetworkGateways/write | Crea o aggiorna un gateway di rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/BastionHosts/action | Ottiene riferimenti Bastion Host in una rete virtuale. |
> | Azione | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Ottiene riferimenti Bastion Host in una rete virtuale. |
> | Azione | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verifica se l’indirizzo IP è disponibile presso la rete virtuale specificata |
> | Azione | Microsoft.Network/virtualNetworks/delete | Elimina una rete virtuale |
> | Azione | Microsoft. Network/virtualNetworks/join/azione | Viene aggiunto a una rete virtuale. Non avvisabile. |
> | Azione | Microsoft.Network/virtualNetworks/peer/action | Associa una rete virtuale a un'altra rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Azione | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Delete | Elimina un criterio dell'endpoint di servizio contestuale |
> | Azione | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Read | Ottiene i criteri dell'endpoint di servizio contestuale |
> | Azione | Microsoft. Network/virtualNetworks/Subnets/contextualServiceEndpointPolicies/Write | Crea un criterio di endpoint di servizio contestuale o aggiorna un criterio di endpoint di servizio contestuale esistente |
> | Azione | Microsoft.Network/virtualNetworks/subnets/delete | Elimina una subnet della rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/subnets/join/action | Viene aggiunto a una rete virtuale. Non avvisabile. |
> | Azione | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non avvisabile. |
> | Azione | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepara una subnet applicando i criteri di rete necessari |
> | Azione | Microsoft.Network/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | Azione | Microsoft. Network/virtualNetworks/Subnets/unprepareNetworkPolicies/Action | Rimuovere la preparazione di una subnet rimuovendo i criteri di rete applicati |
> | Azione | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Ottiene i riferimenti a tutte le macchine virtuali nella subnet della rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/subnets/write | Crea una subnet della rete virtuale o ne aggiorna una esistente |
> | Azione | Microsoft.Network/virtualNetworks/usages/read | Ottiene gli utilizzi IP per ogni subnet della rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/virtualMachines/read | Ottiene i riferimenti a tutte le macchine virtuali in una rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Elimina un peering della rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Ottiene una definizione di peering della rete virtuale |
> | Azione | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Crea un peering della rete virtuale o ne aggiorna uno esistente |
> | Azione | Microsoft.Network/virtualNetworks/write | Crea una rete virtuale o ne aggiorna una esistente |
> | Azione | Microsoft.Network/virtualNetworkTaps/delete | Elimina un TAP di rete virtuale |
> | Azione | Microsoft.Network/virtualNetworkTaps/join/action | Aggiunge un tocco alla rete virtuale. Non avvisabile. |
> | Azione | Microsoft.Network/virtualNetworkTaps/read | Ottiene un TAP di rete virtuale |
> | Azione | Microsoft.Network/virtualNetworkTaps/write | Crea o aggiorna un TAP di rete virtuale |
> | Azione | Microsoft. Network/virtualRouters/Delete | Elimina un VirtualRouter |
> | Azione | Microsoft. Network/virtualRouters/join/azione | Aggiunge un VirtualRouter. Non avvisabile. |
> | Azione | Microsoft. Network/virtualRouters/peering/Delete | Elimina un VirtualRouterPeering |
> | Azione | Microsoft. Network/virtualRouters/peering/lettura | Ottiene un VirtualRouterPeering |
> | Azione | Microsoft. Network/virtualRouters/peering/scrittura | Crea un VirtualRouterPeering o aggiorna un VirtualRouterPeering esistente |
> | Azione | Microsoft. Network/virtualRouters/Read | Ottiene un VirtualRouter |
> | Azione | Microsoft. Network/virtualRouters/Write | Crea un VirtualRouter o aggiorna un VirtualRouter esistente |
> | Azione | Microsoft.Network/virtualWans/delete | Elimina una rete WAN virtuale |
> | Azione | Microsoft. Network/virtualwans/generateVpnProfile/Action | Genera VirtualWanVpnServerConfiguration la |
> | Azione | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Elimina una configurazione di server VPN da punto a sito di una rete WAN virtuale |
> | Azione | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Elimina una configurazione di server VPN PS2 WAN virtuale |
> | Azione | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Crea una configurazione di server VPN da punto a sito di una rete WAN virtuale o ne aggiorna una esistente |
> | Azione | Microsoft.Network/virtualWans/read | Ottiene una rete WAN virtuale |
> | Azione | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Ottiene i provider di sicurezza delle reti WAN virtuali supportati |
> | Azione | Microsoft.Network/virtualWans/virtualHubs/read | Recupera tutti gli hub virtuali che fanno riferimento a una rete WAN virtuale |
> | Azione | Microsoft.Network/virtualwans/vpnconfiguration/action | Ottiene una configurazione VPN |
> | Azione | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | Ottenere VirtualWanVpnServerConfigurations |
> | Azione | Microsoft.Network/virtualWans/vpnSites/read | Recupera tutti i siti VPN che fanno riferimento a una rete WAN virtuale |
> | Azione | Microsoft.Network/virtualWans/write | Crea o aggiorna una rete WAN virtuale |
> | Azione | Microsoft.Network/vpnGateways/delete | Elimina un gateway VPN. |
> | Azione | microsoft.network/vpngateways/listvpnconnectionshealth/action | Ottiene lo stato di integrità della connessione per tutte le connessioni o per un sottoinsieme delle connessioni in un gateway VPN |
> | Azione | Microsoft.Network/vpnGateways/read | Ottiene un gateway VPN. |
> | Azione | microsoft.network/vpngateways/reset/action | Reimposta un gateway VPN |
> | Azione | microsoft.network/vpnGateways/vpnConnections/delete | Elimina una connessione VPN |
> | Azione | microsoft.network/vpnGateways/vpnConnections/read | Ottiene una connessione VPN. |
> | Azione | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Ottiene una connessione a collegamento VPN |
> | Azione | microsoft.network/vpnGateways/vpnConnections/write | Inserisce una connessione VPN. |
> | Azione | Microsoft.Network/vpnGateways/write | Inserisce un gateway VPN. |
> | Azione | Microsoft. Network/vpnServerConfigurations/Delete | Elimina VpnServerConfiguration |
> | Azione | Microsoft. Network/vpnServerConfigurations/Read | Ottenere VpnServerConfiguration |
> | Azione | Microsoft. Network/vpnServerConfigurations/Write | Crea o aggiorna VpnServerConfiguration |
> | Azione | Microsoft.Network/vpnsites/delete | Elimina una risorsa di sito VPN. |
> | Azione | Microsoft.Network/vpnsites/read | Ottiene una risorsa di sito VPN. |
> | Azione | Microsoft. Network/vpnSites/vpnSiteLinks/Read | Ottiene un collegamento di sito VPN |
> | Azione | Microsoft.Network/vpnsites/write | Crea o aggiorna una risorsa di sito VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verifica se il nome di risorsa di uno spazio dei nomi specificato è disponibile o meno all'interno del servizio Hub di notifica. |
> | Azione | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi. |
> | Azione | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita.  |
> | Azione | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Ottiene la stringa di connessione per lo spazio dei nomi |
> | Azione | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi. |
> | Azione | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Regola di autorizzazione dello spazio dei nomi - Rigenera chiave primaria/secondaria. Specificare la chiave da rigenerare |
> | Azione | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Controlla se un determinato nome di hub di notifica è disponibile all'interno di uno spazio dei nomi. |
> | Azione | Microsoft.NotificationHubs/Namespaces/Delete | Elimina la risorsa spazio dei nomi |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Ottiene l'elenco di regole di autorizzazione dell’hub di notifica |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Elimina le regole di autorizzazione dell’hub di notifica |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Ottiene la stringa di connessione all'hub di notifica |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Ottiene l'elenco di regole di autorizzazione dell’hub di notifica |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | La regola di autorizzazione dell’hub di notifica rigenera la chiave primaria/secondaria. Specificare la chiave da rigenerare |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Crea regole di autorizzazione dell'inoltro dell’hub di notifica e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Invia una notifica push di prova. |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Elimina la risorsa nell’hub di notifica |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Ottiene tutte le credenziali PNS dell’hub di notifica. Queste includono le credenziali WNS, MPNS, APNS, GCM e Baidu |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Ottiene l’elenco di descrizioni dell’hub di notifica |
> | Azione | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Crea un hub di notifica e ne aggiorna le proprietà. Le proprietà includono principalmente credenziali PNS, regole di autorizzazione e TTL |
> | Azione | Microsoft.NotificationHubs/Namespaces/read | Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi |
> | Azione | Microsoft.NotificationHubs/Namespaces/write | Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi. |
> | Azione | Microsoft.NotificationHubs/operationResults/read | Restituisce i risultati dell'operazione per il provider di Hub di notifica |
> | Azione | Microsoft.NotificationHubs/operations/read | Restituisce un elenco di operazioni supportate per il provider di Hub di notifica |
> | Azione | Microsoft.NotificationHubs/register/action | Registra la sottoscrizione per il provider di risorse hub di notifica e abilita la creazione di spazi dei nomi e hub di notifica |
> | Azione | Microsoft.NotificationHubs/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse hub di notifica e abilita la creazione di spazi dei nomi e hub di notifica |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.OffAzure/HyperVSites/clusters/read | Ottiene le proprietà di un cluster Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/clusters/write | Crea o aggiorna il cluster Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/delete | Elimina il sito Hyper-V |
> | Azione | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Ottiene il riepilogo di integrità per la risorsa Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/hosts/read | Ottiene le proprietà di un host Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/hosts/write | Crea o aggiorna l'host Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/jobs/read | Ottiene le proprietà dei processi Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/machines/read | Ottiene le proprietà dei computer Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Ottiene le proprietà di uno stato dell'operazione Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/read | Ottiene le proprietà di un sito Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/refresh/action | Aggiorna gli oggetti in un sito Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Ottiene le proprietà di un account RunAs Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/usage/read | Ottiene gli utilizzi di un sito Hyper-V |
> | Azione | Microsoft.OffAzure/HyperVSites/write | Crea o aggiorna il sito Hyper-V |
> | Azione | Microsoft.OffAzure/Operations/read | Legge le operazioni esposte |
> | Azione | Microsoft.OffAzure/register/action | Registra la sottoscrizione per il provider di risorse Microsoft.OffAzure |
> | Azione | Microsoft. OffAzure/ServerSites/Jobs/Read | Ottiene le proprietà di un processo server |
> | Azione | Microsoft. OffAzure/ServerSites/machines/Read | Ottiene le proprietà di un computer server |
> | Azione | Microsoft. OffAzure/ServerSites/machines/Write | Scrivere le proprietà di un computer server |
> | Azione | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Ottiene le proprietà dello stato di un'operazione del server |
> | Azione | Microsoft. OffAzure/ServerSites/Read | Ottiene le proprietà di un sito del server |
> | Azione | Microsoft. OffAzure/ServerSites/Refresh/Action | Aggiorna gli oggetti all'interno di un sito del server |
> | Azione | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Ottiene le proprietà di un account RunAs del server |
> | Azione | Microsoft. OffAzure/ServerSites/Usage/Read | Ottiene gli utilizzi di un sito del server |
> | Azione | Microsoft. OffAzure/ServerSites/Write | Crea o aggiorna il sito del server |
> | Azione | Microsoft.OffAzure/VMwareSites/delete | Elimina il sito VMware |
> | Azione | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Ottiene il riepilogo di integrità per la risorsa VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/jobs/read | Ottiene le proprietà di un processo VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/machines/read | Ottiene le proprietà di un computer VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/machines/start/action | Avvia le macchine VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/machines/stop/action | Arresta i computer VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Ottiene le proprietà di uno stato dell'operazione VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/read | Ottiene le proprietà di un sito VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/refresh/action | Aggiorna gli oggetti in un sito VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Ottiene le proprietà di un account RunAs VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/usage/read | Ottiene gli utilizzi di un sito VMware |
> | Azione | Microsoft.OffAzure/VMwareSites/vcenters/read | Ottiene le proprietà di un VMware vCenter |
> | Azione | Microsoft.OffAzure/VMwareSites/vcenters/write | Crea o aggiorna il VMware vCenter |
> | Azione | Microsoft.OffAzure/VMwareSites/write | Crea o aggiorna il sito VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.OperationalInsights/linkTargets/read | Elenca gli account esistenti non associati a una sottoscrizione di Azure. Per collegare questa sottoscrizione di Azure a un’area di lavoro, utilizzare un ID cliente restituito da questa operazione nella proprietà ID cliente dell’operazione Crea area di lavoro. |
> | Azione | microsoft.operationalinsights/operations/read | Elenca tutte le operazioni API Rest OperationalInsights disponibili. |
> | Azione | microsoft.operationalinsights/register/action | Rergisters la sottoscrizione. |
> | Azione | Microsoft.OperationalInsights/register/action | Registra una sottoscrizione a un provider di risorse. |
> | Azione | microsoft.operationalinsights/unregister/action | Annulla la registrazione della sottoscrizione. |
> | Azione | Microsoft.OperationalInsights/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | Azione | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Ottiene lo schema di ricerca V2. |
> | Azione | Microsoft.OperationalInsights/workspaces/api/query/action | Esegue la ricerca usando il nuovo motore. |
> | Azione | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Ottiene lo schema di ricerca V2. |
> | Azione | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Elimina l'ambito di configurazione |
> | Azione | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Ottiene l'ambito di configurazione |
> | Azione | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Imposta l'ambito di configurazione |
> | Azione | Microsoft.OperationalInsights/workspaces/datasources/delete | Elimina le origini dati in un'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/datasources/read | Ottiene le origini dati in un'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/datasources/write | Crea/Aggiorna le origini dati in un'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/delete | Elimina un'area di lavoro. Se l'area di lavoro era collegata a un’area di lavoro esistente al momento della creazione, l’area di lavoro alla quale era collegata non viene eliminata. |
> | Azione | Microsoft.OperationalInsights/workspaces/gateways/delete | Rimuove un gateway configurato per l'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Genera il certificato di registrazione per l’area di lavoro. Il certificato viene utilizzato per la connessione di Microsoft System Center Operations Manager all'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Disabilita un Intelligence Pack per una specifica area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Abilita un Intelligence Pack per una specifica area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Elenca tutti gli Intelligence Pack visibili per una specifica area di lavoro, oltre a elencare se il pacchetto è abilitato o disabilitato per quell'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Elimina i servizi collegati nell'area di lavoro specificata. |
> | Azione | Microsoft.OperationalInsights/workspaces/linkedServices/read | Ottiene i servizi collegati nell'area di lavoro specificata. |
> | Azione | Microsoft.OperationalInsights/workspaces/linkedServices/write | Crea/Aggiorna i servizi collegati nell'area di lavoro specificata. |
> | Azione | Microsoft.OperationalInsights/workspaces/listKeys/action | Recupera le chiavi di elenco per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/listKeys/read | Recupera le chiavi di elenco per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/managementGroups/read | Ottiene nomi e metadati per i gruppi di gestione di System Center Operations Manager connessi a questa area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Ottiene le definizioni di metrica nell'area di lavoro |
> | Azione | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Elimina le impostazioni di notifica dell'utente per l'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Ottiene le impostazioni di notifica dell'utente per l'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Imposta le impostazioni di notifica dell'utente per l'area di lavoro. |
> | Azione | microsoft.operationalinsights/workspaces/operations/read | Ottiene lo stato di un'operazione dell'area di lavoro OperationalInsights. |
> | Azione | Microsoft.OperationalInsights/workspaces/purge/action | Elimina i dati specificati dall'area di lavoro |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Leggere i dati dalla tabella AADDomainServicesAccountLogon |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Leggere i dati dalla tabella AADDomainServicesAccountManagement |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Leggere i dati dalla tabella AADDomainServicesDirectoryServiceAccess |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Leggere i dati dalla tabella AADDomainServicesLogonLogoff |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Leggere i dati dalla tabella AADDomainServicesPolicyChange |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Leggere i dati dalla tabella AADDomainServicesPrivilegeUse |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Leggere i dati dalla tabella AADDomainServicesSystemSecurity |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Legge i dati dalla tabella ADAssessmentRecommendation |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AddonAzureBackupAlerts/Read | Leggere i dati dalla tabella AddonAzureBackupAlerts |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AddonAzureBackupJobs/Read | Leggere i dati dalla tabella AddonAzureBackupJobs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AddonAzureBackupPolicy/Read | Leggere i dati dalla tabella AddonAzureBackupPolicy |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AddonAzureBackupProtectedInstance/Read | Leggere i dati dalla tabella AddonAzureBackupProtectedInstance |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AddonAzureBackupStorage/Read | Leggere i dati dalla tabella AddonAzureBackupStorage |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Leggere i dati dalla tabella ADFActivityRun |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Leggere i dati dalla tabella ADFPipelineRun |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Leggere i dati dalla tabella ADFTriggerRun |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Legge i dati dalla tabella ADReplicationResult |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Legge i dati dalla tabella ADSecurityAssessmentRecommendation |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AegDeliveryFailureLogs/Read | Leggere i dati dalla tabella AegDeliveryFailureLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AegPublishFailureLogs/Read | Leggere i dati dalla tabella AegPublishFailureLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Alert/read | Legge i dati dalla tabella Alert |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Legge i dati dalla tabella AlertHistory |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AmlComputeClusterEvent/Read | Leggere i dati dalla tabella AmlComputeClusterEvent |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AmlComputeClusterNodeEvent/Read | Leggere i dati dalla tabella AmlComputeClusterNodeEvent |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AmlComputeJobEvent/Read | Leggere i dati dalla tabella AmlComputeJobEvent |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/ApiManagementGatewayLogs/Read | Leggere i dati dalla tabella ApiManagementGatewayLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Legge i dati dalla tabella AppCenterError |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Legge i dati dalla tabella ApplicationInsights |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppPlatformLogsforSpring/Read | Leggere i dati dalla tabella AppPlatformLogsforSpring |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppPlatformSystemLogs/Read | Leggere i dati dalla tabella AppPlatformSystemLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppServiceAppLogs/Read | Leggere i dati dalla tabella AppServiceAppLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppServiceAuditLogs/Read | Leggere i dati dalla tabella AppServiceAuditLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppServiceConsoleLogs/Read | Leggere i dati dalla tabella AppServiceConsoleLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppServiceEnvironmentPlatformLogs/Read | Leggere i dati dalla tabella AppServiceEnvironmentPlatformLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/AppServiceHTTPLogs/Read | Leggere i dati dalla tabella AppServiceHTTPLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Legge i dati della tabella AuditLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Leggere i dati dalla tabella AutoscaleEvaluationsLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Leggere i dati dalla tabella AutoscaleScaleActionsLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Leggere i dati dalla tabella AWSCloudTrail |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Legge i dati dalla tabella AzureActivity |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Leggere i dati dalla tabella AzureAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Legge i dati dalla tabella AzureMetrics |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/BaiClusterEvent/Read | Leggere i dati dalla tabella BaiClusterEvent |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/BaiClusterNodeEvent/Read | Leggere i dati dalla tabella BaiClusterNodeEvent |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/BaiJobEvent/Read | Leggere i dati dalla tabella BaiJobEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Leggere i dati dalla tabella BlockchainApplicationLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Leggere i dati dalla tabella BlockchainProxyLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Legge i dati dalla tabella BoundPort |
> | Azione | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Legge i dati dalla tabella CommonSecurityLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Legge i dati dalla tabella ComputerGroup |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Legge i dati dalla tabella ConfigurationChange |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Legge i dati dalla tabella ConfigurationData |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Legge i dati dalla tabella ContainerImageInventory |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Legge i dati dalla tabella ContainerInventory |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Legge i dati dalla tabella ContainerLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Leggere i dati dalla tabella ContainerNodeInventory |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/ContainerRegistryLoginEvents/Read | Leggere i dati dalla tabella ContainerRegistryLoginEvents |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/ContainerRegistryRepositoryEvents/Read | Leggere i dati dalla tabella ContainerRegistryRepositoryEvents |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Legge i dati dalla tabella ContainerServiceLog |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/CoreAzureBackup/Read | Leggere i dati dalla tabella CoreAzureBackup |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Leggere i dati dalla tabella DatabricksAccounts |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Leggere i dati dalla tabella DatabricksClusters |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Leggere i dati dalla tabella DatabricksDBFS |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/DatabricksInstancePools/Read | Leggere i dati dalla tabella DatabricksInstancePools |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Leggere i dati dalla tabella DatabricksJobs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Leggere i dati dalla tabella DatabricksNotebook |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Leggere i dati dalla tabella DatabricksSecrets |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Leggere i dati dalla tabella DatabricksSQLPermissions |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Leggere i dati dalla tabella DatabricksSSH |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Leggere i dati dalla tabella DatabricksTables |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Leggere i dati dalla tabella DatabricksWorkspace |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Legge i dati dalla tabella DeviceAppCrash |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Legge i dati dalla tabella DeviceAppLaunch |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Legge i dati dalla tabella DeviceCalendar |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Legge i dati dalla tabella DeviceCleanup |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Legge i dati dalla tabella DeviceConnectSession |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Legge i dati dalla tabella DeviceEtw |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Legge i dati dalla tabella DeviceHardwareHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Legge i dati dalla tabella DeviceHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Legge i dati dalla tabella DeviceHeartbeat |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Legge i dati dalla tabella DeviceSkypeHeartbeat |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Legge i dati dalla tabella DeviceSkypeSignIn |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Legge i dati dalla tabella DeviceSleepState |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Legge i dati dalla tabella DHAppFailure |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Legge i dati dalla tabella DHAppReliability |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/DHCPActivity/Read | Leggere i dati dalla tabella DHCPActivity |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Legge i dati dalla tabella DHDriverReliability |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Legge i dati dalla tabella DHLogonFailures |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Legge i dati dalla tabella DHLogonMetrics |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Legge i dati dalla tabella DHOSCrashData |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Legge i dati dalla tabella DHOSReliability |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Legge i dati dalla tabella DHWipAppLearning |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Legge i dati dalla tabella DnsEvents |
> | Azione | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Legge i dati dalla tabella DnsInventory |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Legge i dati dalla tabella ETWEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Event/read | Legge i dati dalla tabella Event |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Legge i dati dalla tabella ExchangeAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Legge i dati dalla tabella ExchangeOnlineAssessmentRecommendation |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/FailedIngestion/Read | Leggere i dati dalla tabella FailedIngestion |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/FunctionAppLogs/Read | Leggere i dati dalla tabella FunctionAppLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Legge i dati dalla tabella Heartbeat |
> | Azione | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Leggere i dati dalla tabella HuntingBookmark |
> | Azione | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Legge i dati dalla tabella IISAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Legge i dati dalla tabella InboundConnection |
> | Azione | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Leggere i dati dalla tabella InsightsMetrics |
> | Azione | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Legge i dati dalla tabella IntuneAuditLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/IntuneDeviceComplianceOrg/Read | Leggere i dati dalla tabella IntuneDeviceComplianceOrg |
> | Azione | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Legge i dati dalla tabella IntuneOperationalLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Legge i dati dalla tabella KubeEvents |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/KubeHealth/Read | Leggere i dati dalla tabella KubeHealth |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/KubeMonAgentEvents/Read | Leggere i dati dalla tabella KubeMonAgentEvents |
> | Azione | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Legge i dati dalla tabella KubeNodeInventory |
> | Azione | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Legge i dati dalla tabella KubePodInventory |
> | Azione | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Legge i dati dalla tabella KubeServices |
> | Azione | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Legge i dati dalla tabella LinuxAuditLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Legge i dati dalla tabella MAApplication |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Legge i dati dalla tabella MAApplicationHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Legge i dati dalla tabella MAApplicationHealthAlternativeVersions |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Legge i dati dalla tabella MAApplicationHealthIssues |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Legge i dati dalla tabella MAApplicationInstance |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Legge i dati dalla tabella MAApplicationInstanceReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Legge i dati dalla tabella MAApplicationReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Legge i dati dalla tabella MADeploymentPlan |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Legge i dati dalla tabella MADevice |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Legge i dati dalla tabella MADeviceNotEnrolled |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Leggere i dati dalla tabella MADeviceNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Legge i dati dalla tabella MADevicePnPHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Legge i dati dalla tabella MADevicePnPHealthAlternativeVersions |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Legge i dati dalla tabella MADevicePnPHealthIssues |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Legge i dati dalla tabella MADeviceReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Legge i dati dalla tabella MADriverInstanceReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Legge i dati dalla tabella MADriverReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Legge i dati dalla tabella MAOfficeAddin |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Leggere i dati dalla tabella MAOfficeAddinEntityHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Legge i dati dalla tabella MAOfficeAddinHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Leggere i dati dalla tabella MAOfficeAddinHealthEventNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Legge i dati dalla tabella MAOfficeAddinHealthIssues |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Legge i dati dalla tabella MAOfficeAddinInstance |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Legge i dati dalla tabella MAOfficeAddinInstanceReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Legge i dati dalla tabella MAOfficeAddinReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Legge i dati dalla tabella MAOfficeApp |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Leggere i dati dalla tabella MAOfficeAppCrashesNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Legge i dati dalla tabella MAOfficeAppHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Legge i dati dalla tabella MAOfficeAppInstance |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Leggere i dati dalla tabella MAOfficeAppInstanceHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Legge i dati dalla tabella MAOfficeAppReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Leggere i dati dalla tabella MAOfficeAppSessionsNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Legge i dati dalla tabella MAOfficeBuildInfo |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Legge i dati dalla tabella MAOfficeCurrencyAssessment |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Legge i dati dalla tabella MAOfficeCurrencyAssessmentDailyCounts |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Legge i dati dalla tabella MAOfficeDeploymentStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Leggere i dati dalla tabella MAOfficeDeploymentStatusNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Leggere i dati dalla tabella MAOfficeMacroErrorNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Leggere i dati dalla tabella MAOfficeMacroGlobalHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Legge i dati dalla tabella MAOfficeMacroHealth |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Legge i dati dalla tabella MAOfficeMacroHealthIssues |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Legge i dati dalla tabella MAOfficeMacroIssueInstanceReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Legge i dati dalla tabella MAOfficeMacroIssueReadiness |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Legge i dati dalla tabella MAOfficeMacroSummary |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Legge i dati dalla tabella MAOfficeSuite |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Legge i dati dalla tabella MAOfficeSuiteInstance |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Legge i dati dalla tabella MAProposedPilotDevices |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Legge i dati dalla tabella MAWindowsBuildInfo |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Legge i dati dalla tabella MAWindowsCurrencyAssessment |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Legge i dati dalla tabella MAWindowsCurrencyAssessmentDailyCounts |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Legge i dati dalla tabella MAWindowsDeploymentStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Leggere i dati dalla tabella MAWindowsDeploymentStatusNRT |
> | Azione | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Legge i dati dalla tabella MAWindowsSysReqInstanceReadiness |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/McasShadowItReporting/Read | Leggere i dati dalla tabella McasShadowItReporting |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftAzureBastionAuditLogs/Read | Leggere i dati dalla tabella MicrosoftAzureBastionAuditLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftDataShareReceivedSnapshotLog/Read | Leggere i dati dalla tabella MicrosoftDataShareReceivedSnapshotLog |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftDataShareSentSnapshotLog/Read | Leggere i dati dalla tabella MicrosoftDataShareSentSnapshotLog |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftDataShareShareLog/Read | Leggere i dati dalla tabella MicrosoftDataShareShareLog |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftDynamicsTelemetryPerformanceLogs/Read | Leggere i dati dalla tabella MicrosoftDynamicsTelemetryPerformanceLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/Read | Leggere i dati dalla tabella MicrosoftDynamicsTelemetrySystemMetricsLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftHealthcareApisAuditLogs/Read | Leggere i dati dalla tabella MicrosoftHealthcareApisAuditLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/MicrosoftInsightsAzureActivityLog/Read | Leggere i dati dalla tabella MicrosoftInsightsAzureActivityLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Legge i dati dalla tabella NetworkMonitoring |
> | Azione | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Legge i dati dalla tabella OfficeActivity |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Operation/read | Legge i dati dalla tabella Operation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Legge i dati dalla tabella OutboundConnection |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Perf/read | Legge i dati dalla tabella Perf |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Legge i dati dalla tabella ProtectionStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Legge i dati dalla tabella ReservedCommonFields |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Legge i dati dalla tabella SCCMAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Legge i dati dalla tabella SCOMAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Legge i dati dalla tabella SecurityAlert |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Legge i dati dalla tabella SecurityBaseline |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Legge i dati dalla tabella SecurityBaselineSummary |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Legge i dati dalla tabella SecurityDetection |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Legge i dati dalla tabella SecurityEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Leggere i dati dalla tabella SecurityIoTRawEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Leggere i dati dalla tabella SecurityRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Legge i dati dalla tabella ServiceFabricOperationalEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Legge i dati dalla tabella ServiceFabricReliableActorEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Legge i dati dalla tabella ServiceFabricReliableServiceEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Legge i dati dalla tabella SfBAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Legge i dati dalla tabella SfBOnlineAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Legge i dati dalla tabella SharePointOnlineAssessmentRecommendation |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/SignalRServiceDiagnosticLogs/Read | Leggere i dati dalla tabella SignalRServiceDiagnosticLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Legge i dati della tabella SigninLogs |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Legge i dati dalla tabella SPAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Legge i dati dalla tabella SQLAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Legge i dati dalla tabella SQLQueryPerformance |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Leggere i dati dalla tabella SqlThreatProtectionLoginAudits |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Leggere i dati dalla tabella SqlVulnerabilityAssessmentResult |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/StorageBlobLogs/Read | Leggere i dati dalla tabella StorageBlobLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/StorageFileLogs/Read | Leggere i dati dalla tabella StorageFileLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/StorageQueueLogs/Read | Leggere i dati dalla tabella StorageQueueLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/StorageTableLogs/Read | Leggere i dati dalla tabella StorageTableLogs |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/SucceededIngestion/Read | Leggere i dati dalla tabella SucceededIngestion |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Legge i dati dalla tabella Syslog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Legge i dati dalla tabella SysmonEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Legge i dati da qualsiasi log personalizzato |
> | Azione | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Leggere i dati dalla tabella ThreatIntelligenceIndicator |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Legge i dati dalla tabella UAApp |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Legge i dati dalla tabella UAComputer |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Legge i dati dalla tabella UAComputerRank |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Legge i dati dalla tabella UADriver |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Legge i dati dalla tabella UADriverProblemCodes |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Legge i dati dalla tabella UAFeedback |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Legge i dati dalla tabella UAHardwareSecurity |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Legge i dati dalla tabella UAIESiteDiscovery |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Legge i dati dalla tabella UAOfficeAddIn |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Leggere dati dalla tabella UAProposedActionPlan |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Legge i dati dalla tabella UASysReqIssue |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Legge i dati dalla tabella UAUpgradedComputer |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Update/read | Legge i dati dalla tabella Update |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Legge i dati dalla tabella UpdateRunProgress |
> | Azione | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Legge i dati dalla tabella UpdateSummary |
> | Azione | Microsoft.OperationalInsights/workspaces/query/Usage/read | Legge i dati dalla tabella Usage |
> | Azione | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Legge i dati dalla tabella VMBoundPort |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/VMComputer/Read | Leggere i dati dalla tabella VMComputer |
> | Azione | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Legge i dati dalla tabella VMConnection |
> | Azione | Microsoft. OperationalInsights/Workspaces/query/VMProcess/Read | Leggere i dati dalla tabella VMProcess |
> | Azione | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Legge i dati dalla tabella W3CIISLog |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Legge i dati dalla tabella WaaSDeploymentStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Legge i dati dalla tabella WaaSInsiderStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Legge i dati dalla tabella WaaSUpdateStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Legge i dati dalla tabella WDAVStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Legge i dati dalla tabella WDAVThreat |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Legge i dati dalla tabella WindowsClientAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Legge i dati della tabella WindowsEvent |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Legge i dati dalla tabella WindowsFirewall |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Legge i dati dalla tabella WindowsServerAssessmentRecommendation |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WireData/read | Legge i dati dalla tabella WireData |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Leggere i dati dalla tabella WorkloadMonitoringPerf |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Legge i dati dalla tabella WUDOAggregatedStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Legge i dati dalla tabella WUDOStatus |
> | Azione | Microsoft.OperationalInsights/workspaces/read | Ottiene un'area di lavoro esistente |
> | Azione | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Rigenera la chiave condivisa dell'area di lavoro specificata |
> | Azione | microsoft.operationalinsights/workspaces/rules/read | Recupera tutte le regole di avviso. |
> | Azione | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Elimina una query di ricerca salvata |
> | Azione | Microsoft.OperationalInsights/workspaces/savedSearches/read | Ottiene una query di ricerca salvata |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/results/read | Recupera i risultati delle ricerche salvati. Funzionalità deprecate |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Elimina le azioni pianificate di ricerca. |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Recupera le azioni pianificate di ricerca. |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Crea o aggiorna le azioni pianificate di ricerca. |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Elimina le ricerche pianificate. |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Recupera le ricerche pianificate. |
> | Azione | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Crea o aggiorna le ricerche pianificate. |
> | Azione | Microsoft.OperationalInsights/workspaces/savedSearches/write | Crea una query di ricerca salvata |
> | Azione | Microsoft.OperationalInsights/workspaces/schema/read | Ottiene lo schema di ricerca per l'area di lavoro.  Lo schema di ricerca include i campi esposti e i relativi tipi. |
> | Azione | Microsoft.OperationalInsights/workspaces/search/action | Esegue una query di ricerca |
> | Azione | microsoft.operationalinsights/workspaces/search/read | Ottiene i risultati della ricerca. Operazione deprecata. |
> | Azione | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Elimina una configurazione di archiviazione. Questa operazione interrompe la lettura dei dati dall’account di archiviazione da parte di Microsoft Operational Insights. |
> | Azione | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Ottiene una configurazione di archiviazione. |
> | Azione | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Crea una nuova configurazione di archiviazione. Tali configurazioni servono per estrarre i dati da una posizione in un account di archiviazione esistente. |
> | Azione | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Ottiene il log di errore ricerca aggiornamento traduzione per l'area di lavoro |
> | Azione | Microsoft.OperationalInsights/workspaces/usages/read | Ottiene i dati di utilizzo di un'area di lavoro tra cui la quantità di dati letti dall'area di lavoro. |
> | Azione | Microsoft.OperationalInsights/workspaces/write | Crea una nuova area di lavoro o collega a un'area di lavoro esistente fornendo l'ID cliente dall’area di lavoro esistente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.OperationsManagement/managementAssociations/delete | Elimina un'associazione di gestione esistente |
> | Azione | Microsoft.OperationsManagement/managementAssociations/read | Ottiene un'associazione di gestione esistente |
> | Azione | Microsoft.OperationsManagement/managementAssociations/write | Crea una nuova associazione di gestione |
> | Azione | Microsoft.OperationsManagement/managementConfigurations/delete | Elimina una configurazione di gestione esistente |
> | Azione | Microsoft.OperationsManagement/managementConfigurations/read | Ottiene una configurazione di gestione esistente |
> | Azione | Microsoft.OperationsManagement/managementConfigurations/write | Crea una nuova configurazione di gestione |
> | Azione | Microsoft.OperationsManagement/register/action | Registra una sottoscrizione a un provider di risorse. |
> | Azione | Microsoft.OperationsManagement/solutions/delete | Elimina soluzione OMS esistente |
> | Azione | Microsoft.OperationsManagement/solutions/read | Ottiene soluzione OMS esistente |
> | Azione | Microsoft.OperationsManagement/solutions/write | Crea una nuova soluzione OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.PolicyInsights/asyncOperationResults/read | Recupera il risultato dell'operazione asincrona. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Verificare lo stato di conformità di un determinato componente in base ai criteri di dati. |
> | Azione | Microsoft.PolicyInsights/operations/read | Ottiene le operazioni supportate nello spazio dei nomi Microsoft. PolicyInsights |
> | DataAction | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Registrare gli eventi del criterio componente risorsa. |
> | Azione | Microsoft.PolicyInsights/policyEvents/queryResults/action | Esegue query sulle informazioni sugli eventi relativi ai criteri. |
> | Azione | Microsoft.PolicyInsights/policyEvents/queryResults/read | Esegue query sulle informazioni sugli eventi relativi ai criteri. |
> | Azione | Microsoft. PolicyInsights/policyMetadata/Read | Ottenere le risorse di metadati dei criteri. |
> | Azione | Microsoft.PolicyInsights/policyStates/queryResults/action | Esegue query sulle informazioni relative agli stati dei criteri. |
> | Azione | Microsoft.PolicyInsights/policyStates/queryResults/read | Esegue query sulle informazioni relative agli stati dei criteri. |
> | Azione | Microsoft.PolicyInsights/policyStates/summarize/action | Esegue query sulle informazioni di riepilogo relative agli stati più recenti dei criteri. |
> | Azione | Microsoft.PolicyInsights/policyStates/summarize/read | Esegue query sulle informazioni di riepilogo relative agli stati più recenti dei criteri. |
> | Azione | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Attiva una nuova valutazione di conformità per l'ambito selezionato. |
> | Azione | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Esegue una query sulle informazioni relative alle risorse richieste dai criteri DeployIfNotExists. |
> | Azione | Microsoft.PolicyInsights/register/action | Registra il provider di risorse Microsoft Policy Insights e ne Abilita le azioni. |
> | Azione | Microsoft.PolicyInsights/remediations/cancel/action | Annulla le correzioni dei criteri Microsoft in corso. |
> | Azione | Microsoft.PolicyInsights/remediations/delete | Elimina le correzioni dei criteri. |
> | Azione | Microsoft.PolicyInsights/remediations/listDeployments/read | Elenca le distribuzioni richieste da una correzione dei criteri. |
> | Azione | Microsoft.PolicyInsights/remediations/read | Ottiene le correzioni dei criteri. |
> | Azione | Microsoft.PolicyInsights/remediations/write | Crea o aggiorna le correzioni dei criteri Microsoft. |
> | Azione | Microsoft.PolicyInsights/unregister/action | Annulla la registrazione del provider di risorse Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Portal/consoles/delete | Rimuove l'istanza di Cloud Shell. |
> | Azione | Microsoft.Portal/consoles/read | Legge l'istanza di Cloud Shell. |
> | Azione | Microsoft.Portal/consoles/write | Crea o aggiorna un'istanza di Cloud Shell. |
> | Azione | Microsoft.Portal/dashboards/delete | Rimuove il dashboard dalla sottoscrizione. |
> | Azione | Microsoft.Portal/dashboards/read | Legge i dashboard nella sottoscrizione. |
> | Azione | Microsoft.Portal/dashboards/write | Aggiunge o modifica un dashboard in una sottoscrizione. |
> | Azione | Microsoft.Portal/register/action | Registra al portale |
> | Azione | Microsoft.Portal/usersettings/delete | Rimuove le impostazioni utente Cloud Shell. |
> | Azione | Microsoft.Portal/usersettings/read | Legge le impostazioni utente Cloud Shell. |
> | Azione | Microsoft.Portal/usersettings/write | Crea o aggiorna Cloud Shell impostazione utente. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.PowerBIDedicated/capacities/delete | Elimina la capacità dedicata Power BI. |
> | Azione | Microsoft.PowerBIDedicated/capacities/read | Recupera le informazioni della capacità dedicata di Power BI specificata. |
> | Azione | Microsoft.PowerBIDedicated/capacities/resume/action | Riprende la capacità. |
> | Azione | Microsoft.PowerBIDedicated/capacities/skus/read | Recupera le informazioni sugli SKU disponibili per la capacità. |
> | Azione | Microsoft.PowerBIDedicated/capacities/suspend/action | Sospende la capacità. |
> | Azione | Microsoft.PowerBIDedicated/capacities/write | Crea o aggiorna la capacità dedicata di Power BI specificata. |
> | Azione | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Controlla che il nome di capacità dedicata di Power BI sia valido e non in uso. |
> | Azione | Microsoft.PowerBIDedicated/locations/operationresults/read | Recupera le informazioni del risultato dell'operazione specificata. |
> | Azione | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Recupera le informazioni dello stato dell'operazione specificata. |
> | Azione | Microsoft.PowerBIDedicated/operations/read | Recupera le informazioni delle operazioni |
> | Azione | Microsoft.PowerBIDedicated/register/action | Registra il provider di risorse di Power BI dedicato. |
> | Azione | Microsoft.PowerBIDedicated/skus/read | Recupera le informazioni degli SKU |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | Azione | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
> | Azione | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Azione | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Crea un elemento protetto di backup |
> | Azione | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti. |
> | Azione | Microsoft.RecoveryServices/Locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | Azione | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Convalida le funzioni |
> | Azione | Microsoft.RecoveryServices/locations/checkNameAvailability/action | CheckNameAvailability è un'API per verificare se il nome di risorsa è disponibile |
> | Azione | Microsoft.RecoveryServices/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | Azione | Microsoft.RecoveryServices/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | Azione | Microsoft.RecoveryServices/register/action | Registra la sottoscrizione per il provider di risorse specificato |
> | Azione | Microsoft.RecoveryServices/Vaults/backupconfig/read | Restituisce la configurazione dell'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupconfig/write | Aggiorna la configurazione dell'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft. RecoveryServices/Vaults/backupEncryptionConfigs/Read | Ottiene la configurazione della crittografia delle risorse di backup. |
> | Azione | Microsoft. RecoveryServices/Vaults/backupEncryptionConfigs/Write | Aggiorna la configurazione della crittografia delle risorse di backup |
> | Azione | Microsoft.RecoveryServices/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Elimina una finalità di protezione del backup |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Ottiene una finalità di protezione del backup |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea un programma di protezione del backup |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Restituisce lo stato dell'operazione |
> | Azione | Microsoft. RecoveryServices/Vaults/backupFabrics/operationsStatus/Read | Restituisce lo stato dell'operazione |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Ottiene tutti i contenitori che si possono proteggere |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Elimina il contenitore registrato |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Esegue operazioni di richiesta di informazioni per i carichi di lavoro all'interno di un contenitore |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Ottiene tutti gli elementi in un contenitore |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | Azione | Microsoft. RecoveryServices/Vaults/backupFabrics/protectionContainers/operationsStatus/Read | Ottiene lo stato dell'operazione eseguita sul contenitore di protezione. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Esegue il backup dell'elemento protetto. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Elimina l'elemento protetto |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Effettua il provisioning del ripristino elementi immediato per l'elemento protetto |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Ottiene i punti di ripristino degli elementi protetti. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Ripristina i punti di ripristino degli elementi protetti. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca il ripristino elementi immediato per l'elemento protetto |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protetto di backup |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Restituisce tutti i contenitori registrati |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Crea un contenitore registrato |
> | Azione | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aggiorna l'elenco di contenitori |
> | Azione | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Annulla il processo |
> | Azione | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Restituisce il risultato dell'operazione di processo. |
> | Azione | Microsoft. RecoveryServices/Vaults/backupJobs/operationsStatus/Read | Restituisce lo stato dell'operazione di processo. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupJobs/read | Restituisce tutti gli oggetti processo |
> | Azione | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Esporta processi |
> | Azione | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Restituisce il risultato dell'operazione di backup di un insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Elimina i criteri di protezione |
> | Azione | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Ottiene i risultati dell'operazione sui criteri. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Ottiene lo stato dell'operazione sui criteri. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | Azione | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crea i criteri di protezione |
> | Azione | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Restituisce l'elenco di tutti gli elementi da proteggere. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | Azione | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | Azione | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Restituisce le informazioni sul PIN di sicurezza dell'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Restituisce la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Aggiorna la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Convalida l'operazione sull'elemento protetto |
> | Azione | Microsoft.RecoveryServices/Vaults/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali per la risorsa e/o l'insieme di credenziali. |
> | Azione | Microsoft.RecoveryServices/Vaults/delete | L'operazione Elimina insieme di credenziali elimina la risorsa di Azure specificata di tipo 'vault' |
> | Azione | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Azione | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Azione | Microsoft.RecoveryServices/Vaults/extendedInformation/write | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Azione | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | Azione | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Ottiene la configurazione delle notifiche dell'insieme di credenziali dei servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configura le notifiche di posta elettronica all'insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Azione | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | L'operazione Annulla registrazione contenitore di servizi può essere usata per annullare la registrazione di un contenitore. |
> | Azione | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | Azione | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | Azione | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | L'operazione Registra contenitore di servizi può essere usata per registrare un contenitore con il servizio di ripristino. |
> | Azione | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Legge le impostazioni degli avvisi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Crea o aggiorna le impostazioni degli avvisi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationEvents/read | Legge gli eventi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica la coerenza dell'infrastruttura |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Elimina tutte le infrastrutture |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Distribuisce immagine del server di elaborazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Esegue la migrazione dell'infrastruttura ad Azure AD |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sulle infrastrutture delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Legge le infrastrutture |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Riassocia gateway |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Rimuove l'infrastruttura |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Rinnova il certificato per l'infrastruttura |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Legge tutte le reti logiche |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Legge le reti |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Elimina tutti i mapping di rete |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Legge i mapping di rete |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Crea o aggiorna tutti i mapping di rete |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Individua elemento da proteggere |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationProtectionContainers/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sui contenitori di protezione delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Legge i contenitori di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Rimuove il contenitore di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Elimina tutti gli elementi della migrazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Esegue la migrazione degli elementi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Legge i punti di ripristino della migrazione |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sugli elementi di migrazione delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Legge tutti gli elementi della migrazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Testa la migrazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Testa la pulizia della migrazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Crea o aggiorna tutti gli elementi della migrazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Legge gli elementi da proteggere |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Aggiungere dischi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Applica punto di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Elimina tutti gli elementi protetti |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Commit del failover |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sugli elementi protetti da risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover pianificato |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Legge gli elementi protetti |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Legge i punti di ripristino di replica |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Rimuove l'elemento protetto |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Rimuovi dischi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Ripristina replica |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Riprotegge l'elemento protetto |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Invia feedback |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Legge le dimensioni di calcolo di destinazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover di test |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Pulizia del failover di test |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aggiorna servizio Mobility |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Crea o aggiorna gli elementi protetti |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Elimina i mapping dei contenitori di protezione |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sui mapping del contenitore di protezione delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Legge i mapping dei contenitori di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Rimuove il mapping di contenitore di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Crea o aggiorna i mapping dei contenitori di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Cambia il contenitore di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Crea o aggiorna i contenitori di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Elimina tutti i provider dei servizi di ripristino |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sui provider di servizi di ripristino delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Legge i provider dei servizi di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aggiorna i provider |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Rimuove tutti i provider dei servizi di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Crea o aggiorna tutti i provider di Servizi di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Legge le classificazioni di archiviazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Elimina tutti i mapping di classificazioni di archiviazione |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sui mapping della classificazione di archiviazione delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Legge i mapping delle classificazioni di archiviazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Crea o aggiorna tutti i mapping di classificazioni di archiviazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Elimina un vCenter |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationvCenters/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sulla risorsa vCenter |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Legge i vCenter |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Crea o aggiorna i vCenters |
> | Azione | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Crea o aggiorna tutte le infrastrutture |
> | Azione | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Annulla processo |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationJobs/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona nei processi delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationJobs/read | Legge i processi |
> | Azione | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Riavvia processo |
> | Azione | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Riprende il processo |
> | Azione | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Legge tutti gli elementi della migrazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Legge i mapping di rete |
> | Azione | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Legge le reti |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationOperationStatus/Read | Legge lo stato dell'operazione di replica dell'insieme di credenziali |
> | Azione | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Elimina i criteri |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationPolicies/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sui criteri delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Legge i criteri |
> | Azione | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Crea o aggiorna i criteri |
> | Azione | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Legge gli elementi protetti |
> | Azione | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Legge i mapping dei contenitori di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Legge i contenitori di protezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Elimina i piani di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Piano di ripristino del commit del failover |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationRecoveryPlans/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sui piani di ripristino delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Piano di ripristino del failover pianificato |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Legge i piani di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Piano di ripristino di riprotezione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Piano di ripristino del failover di test |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Piano di ripristino della pulizia del failover di test |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Piano di ripristino del failover |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Crea o aggiorna i piani di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Legge i provider dei servizi di ripristino |
> | Azione | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Legge i mapping delle classificazioni di archiviazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Legge le classificazioni di archiviazione |
> | Azione | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Legge i sistemi operativi supportati  |
> | Azione | Microsoft.RecoveryServices/vaults/replicationUsages/read | Legge tutti gli utilizzi della replica dell'insieme di credenziali |
> | Azione | Microsoft. RecoveryServices/Vaults/replicationVaultHealth/operationresults/Read | Tenere traccia dei risultati di un'operazione asincrona sull'integrità della replica dell'insieme di credenziali delle risorse |
> | Azione | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Legge qualsiasi stato di replica dell'insieme di credenziali |
> | Azione | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Aggiorna l’integrità dell'insieme di credenziali |
> | Azione | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Legge i sistemi operativi supportati  |
> | Azione | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Crea o aggiorna qualsiasi  |
> | Azione | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Legge i vCenter |
> | Azione | Microsoft.RecoveryServices/vaults/usages/read | Legge tutti gli utilizzi dell'insieme di credenziali |
> | Azione | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | Azione | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | Azione | Microsoft.RecoveryServices/Vaults/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Relay/checkNameAvailability/action | Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. |
> | Azione | Microsoft.Relay/checkNamespaceAvailability/action | Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. Questa API è deprecata, usare invece CheckNameAvailability. |
> | Azione | Microsoft.Relay/namespaces/authorizationRules/action | Aggiorna la regola di autorizzazione dello spazio dei nomi. Questa API è deprecata. Usare invece una chiamata PUT per aggiornare la regola di autorizzazione dello spazio dei nomi... Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.Relay/namespaces/authorizationRules/delete | Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita.  |
> | Azione | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Ottiene la stringa di connessione per lo spazio dei nomi |
> | Azione | Microsoft.Relay/namespaces/authorizationRules/read | Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi. |
> | Azione | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.Relay/namespaces/authorizationRules/write | Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.Relay/namespaces/Delete | Elimina la risorsa spazio dei nomi |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Ottiene le chiavi delle regole di autorizzazione per lo spazio dei nomi primario di ripristino di emergenza |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Ottiene le regole di autorizzazione dello spazio dei nomi primario di ripristino di emergenza |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Disabilita il ripristino di emergenza e arresta la replica delle modifiche dagli spazi dei nomi primari a quelli secondari. |
> | Azione | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica la disponibilità di alias dello spazio dei nomi nella sottoscrizione specificata. |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Elimina la configurazione di ripristino di emergenza associata allo spazio dei nomi. Questa operazione può essere chiamata solo tramite lo spazio dei nomi primario. |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Chiama il failover di ripristino di emergenza con ridondanza geografica e riconfigura l'alias dello spazio dei nomi affinché punti allo spazio dei nomi secondario. |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Recupera la configurazione di ripristino di emergenza associata allo spazio dei nomi. |
> | Azione | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Crea o aggiorna la configurazione di ripristino di emergenza associata allo spazio dei nomi. |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operazione per aggiornare una connessione ibrida. Questa operazione non è supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione. |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operazione per l'eliminazione delle regole di autorizzazione della connessione ibrida |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Ottiene la stringa di connessione per la connessione ibrida |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Ottiene l'elenco delle regole di autorizzazione della connessione ibrida |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Crea regole di autorizzazione della connessione ibrida e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/Delete | Operazione per l'eliminazione di risorse di connessione ibrida |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/read | Ottiene l'elenco delle descrizioni delle risorse della connessione ibrida |
> | Azione | Microsoft.Relay/namespaces/HybridConnections/write | Crea o aggiorna proprietà della connessione ibrida. |
> | Azione | Microsoft.Relay/namespaces/messagingPlan/read | Ottiene il piano di messaggistica per uno spazio dei nomi.<br>Questa API è deprecata.<br>Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive.<br>Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.Relay/namespaces/messagingPlan/write | Aggiorna il piano di messaggistica per uno spazio dei nomi.<br>Questa API è deprecata.<br>Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive.<br>Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.Relay/namespaces/operationresults/read | Operazione di recupero dello stato dello spazio dei nomi |
> | Azione | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Read | Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi |
> | Azione | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/diagnosticSettings/Write | Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi |
> | Azione | Microsoft. Relay/Namespaces/Providers/Microsoft. Insights/logDefinitions/Read | Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi |
> | Azione | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi |
> | Azione | Microsoft.Relay/namespaces/read | Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi |
> | Azione | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Rimuove uno spazio dei nomi ACS |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operazione per aggiornare un inoltro WCF. Questa operazione non è supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione. |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operazione per l'eliminazione di regole di autorizzazione dell'inoltro WCF |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Ottiene la stringa di connessione per l'inoltro WCF |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Ottiene l'elenco delle regole di autorizzazione dell'inoltro WCF |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Crea regole di autorizzazione dell'inoltro WCF e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/Delete | Operazione per l'eliminazione delle risorse dell'inoltro WCF |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/read | Ottiene l'elenco delle descrizioni delle risorse dell'inoltro WCF |
> | Azione | Microsoft.Relay/namespaces/WcfRelays/write | Crea o aggiorna proprietà inoltro WCF. |
> | Azione | Microsoft.Relay/namespaces/write | Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi. |
> | Azione | Microsoft.Relay/operations/read | Ottiene operazioni |
> | Azione | Microsoft.Relay/register/action | Registra la sottoscrizione per il provider di risorse inoltro e abilita la creazione di risorse inoltro |
> | Azione | Microsoft.Relay/unregister/action | Registra la sottoscrizione per il provider di risorse inoltro e abilita la creazione di risorse inoltro |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Ottiene lo stato di disponibilità per la risorsa specificata |
> | Azione | Microsoft.ResourceHealth/AvailabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Azione | Microsoft.ResourceHealth/events/read | Recupera gli eventi di integrità dei servizi per la sottoscrizione specificata |
> | Azione | Microsoft.Resourcehealth/healthevent/action | Indica la variazione dello stato di integrità per la risorsa specificata |
> | Azione | Microsoft.Resourcehealth/healthevent/Activated/action | Indica la variazione dello stato di integrità per la risorsa specificata |
> | Azione | Microsoft.Resourcehealth/healthevent/InProgress/action | Indica la variazione dello stato di integrità per la risorsa specificata |
> | Azione | Microsoft.Resourcehealth/healthevent/Pending/action | Indica la variazione dello stato di integrità per la risorsa specificata |
> | Azione | Microsoft.Resourcehealth/healthevent/Resolved/action | Indica la variazione dello stato di integrità per la risorsa specificata |
> | Azione | Microsoft.Resourcehealth/healthevent/Updated/action | Indica la variazione dello stato di integrità per la risorsa specificata |
> | Azione | Microsoft.ResourceHealth/impactedResources/read | Recupera le risorse interessate per la sottoscrizione specificata |
> | Azione | Microsoft.ResourceHealth/metadata/read | Ottiene i metadati |
> | Azione | Microsoft. ResourceHealth/Notifications/Read | Riceve notifiche Azure Resource Manager |
> | Azione | Microsoft.ResourceHealth/Operations/read | Recupera le operazioni disponibili per Integrità risorse Microsoft |
> | Azione | Microsoft.ResourceHealth/register/action | Registra la sottoscrizione per Microsoft ResourceHealth |
> | Azione | Microsoft.ResourceHealth/unregister/action | Annulla la registrazione della sottoscrizione per Integrità risorse Microsoft |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. resources/calculateTemplateHash/Action | Calcolare l'hash del modello fornito. |
> | Azione | Microsoft. resources/checkPolicyCompliance/Read | Controlla lo stato di conformità della risorsa specificata in base ai criteri della risorsa. |
> | Azione | Microsoft.Resources/checkResourceName/action | Verifica la validità del nome della risorsa. |
> | Azione | Microsoft.Resources/deployments/cancel/action | Annulla una distribuzione. |
> | Azione | Microsoft.Resources/deployments/delete | Elimina una distribuzione. |
> | Azione | Microsoft. resources/Distributions/exportTemplate/Action | Esportare il modello per una distribuzione |
> | Azione | Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Azione | Microsoft. resources/Distributions/operationstatuses/Read | Ottiene o elenca gli stati dell'operazione di distribuzione. |
> | Azione | Microsoft.Resources/deployments/read | Ottiene o elenca le distribuzioni. |
> | Azione | Microsoft.Resources/deployments/validate/action | Convalida una distribuzione. |
> | Azione | Microsoft.Resources/deployments/whatIf/action | Stima le modifiche della distribuzione del modello. |
> | Azione | Microsoft.Resources/deployments/write | Crea o aggiorna una distribuzione. |
> | Azione | Microsoft. resources/deploymentScripts/Delete | Elimina uno script di distribuzione |
> | Azione | Microsoft. resources/deploymentScripts/logs/lettura | Ottiene o elenca i log di script di distribuzione |
> | Azione | Microsoft. resources/deploymentScripts/Read | Ottiene o elenca gli script di distribuzione |
> | Azione | Microsoft. resources/deploymentScripts/Write | Crea o aggiorna uno script di distribuzione |
> | Azione | Microsoft.Resources/links/delete | Elimina un collegamento a una risorsa. |
> | Azione | Microsoft.Resources/links/read | Ottiene o elenca i collegamenti a una risorsa. |
> | Azione | Microsoft.Resources/links/write | Crea o aggiorna un collegamento a una risorsa. |
> | Azione | Microsoft.Resources/marketplace/purchase/action | Permette di acquistare una risorsa dal marketplace. |
> | Azione | Microsoft.Resources/providers/read | Ottiene l'elenco dei provider. |
> | Azione | Microsoft.Resources/resources/read | Ottiene l'elenco delle risorse in base a filtri. |
> | Azione | Microsoft.Resources/subscriptions/locations/read | Ottiene l'elenco delle posizioni supportate. |
> | Azione | Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/providers/read | Ottiene o elenca i provider di risorse. |
> | Azione | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Azione | Microsoft.Resources/subscriptions/resourceGroups/delete | Elimina un gruppo di risorse e tutte le risorse in esso contenute. |
> | Azione | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Azione | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Ottiene o elenca gli stati dell'operazione di distribuzione. |
> | Azione | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Ottiene o elenca le distribuzioni. |
> | Azione | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Crea o aggiorna una distribuzione. |
> | Azione | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Sposta le risorse da un gruppo di risorse a un altro. |
> | Azione | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Azione | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Ottiene le risorse del gruppo di risorse. |
> | Azione | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Convalida lo spostamento di risorse da un gruppo di risorse a un altro. |
> | Azione | Microsoft.Resources/subscriptions/resourceGroups/write | Crea o aggiorna un gruppo di risorse. |
> | Azione | Microsoft.Resources/subscriptions/resources/read | Ottiene le risorse di una sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/tagNames/delete | Elimina una categoria della sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/tagNames/read | Ottiene o elenca le categorie della sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Elimina un valore di categoria della sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Ottiene o elenca i valori delle categorie della sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Aggiunge un valore di categoria della sottoscrizione. |
> | Azione | Microsoft.Resources/subscriptions/tagNames/write | Aggiunge una categoria della sottoscrizione. |
> | Azione | Microsoft.Resources/tags/delete | Rimuove tutti i tag in una risorsa. |
> | Azione | Microsoft.Resources/tags/read | Ottiene tutti i tag in una risorsa. |
> | Azione | Microsoft.Resources/tags/write | Aggiorna i tag in una risorsa sostituendo o unendo i tag esistenti con un nuovo set di tag o rimuovendo i tag esistenti. |
> | Azione | Microsoft.Resources/tenants/read | Ottiene l'elenco dei tenant. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Scheduler/jobcollections/delete | Elimina la raccolta processi. |
> | Azione | Microsoft.Scheduler/jobcollections/disable/action | Disabilita la raccolta processi. |
> | Azione | Microsoft.Scheduler/jobcollections/enable/action | Abilita la raccolta processi. |
> | Azione | Microsoft.Scheduler/jobcollections/jobs/delete | Elimina il processo. |
> | Azione | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Genera la definizione dell'app per la logica in base a un processo dell'Utilità di pianificazione. |
> | Azione | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Ottiene la cronologia processi. |
> | Azione | Microsoft.Scheduler/jobcollections/jobs/read | Ottiene il processo. |
> | Azione | Microsoft.Scheduler/jobcollections/jobs/run/action | Esegue il processo. |
> | Azione | Microsoft.Scheduler/jobcollections/jobs/write | Crea o aggiorna il processo. |
> | Azione | Microsoft.Scheduler/jobcollections/read | Ottiene la raccolta processi |
> | Azione | Microsoft.Scheduler/jobcollections/write | Crea o aggiorna la raccolta processi. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Search/checkNameAvailability/action | Controlla la disponibilità del nome del servizio. |
> | Azione | Microsoft.Search/operations/read | Elenca tutte le operazioni disponibili del provider Microsoft.Search. |
> | Azione | Microsoft.Search/register/action | Registra la sottoscrizione per il provider di risorse di ricerca e consente la creazione di servizi di ricerca. |
> | Azione | Microsoft.Search/searchServices/createQueryKey/action | Crea la chiave di query. |
> | Azione | Microsoft.Search/searchServices/delete | Elimina il servizio di ricerca. |
> | Azione | Microsoft.Search/searchServices/deleteQueryKey/delete | Elimina la chiave di query. |
> | Azione | Microsoft.Search/searchServices/listAdminKeys/action | Legge le chiavi di amministrazione. |
> | Azione | Microsoft. search/searchServices/listQueryKeys/Action | Restituisce l'elenco delle chiavi API di query per il servizio Ricerca di Azure specificato. |
> | Azione | Microsoft.Search/searchServices/listQueryKeys/read | Restituisce l'elenco delle chiavi API di query per il servizio Ricerca di Azure specificato. |
> | Azione | Microsoft. search/searchServices/privateEndpointConnectionProxies/Delete | Elimina un proxy di connessione all'endpoint privato esistente |
> | Azione | Microsoft. search/searchServices/privateEndpointConnectionProxies/Read | Restituisce l'elenco dei proxy di connessione all'endpoint privato o ottiene le proprietà per il proxy di connessione all'endpoint privato specificato |
> | Azione | Microsoft. search/searchServices/privateEndpointConnectionProxies/Validate/Action | Convalida una chiamata di creazione di una connessione all'endpoint privato dal lato NRP |
> | Azione | Microsoft. search/searchServices/privateEndpointConnectionProxies/Write | Crea un proxy di connessione all'endpoint privato con i parametri specificati o aggiorna le proprietà o i tag per il proxy di connessione all'endpoint privato specificato |
> | Azione | Microsoft.Search/searchServices/read | Legge il servizio di ricerca. |
> | Azione | Microsoft.Search/searchServices/regenerateAdminKey/action | Rigenera la chiave di amministrazione. |
> | Azione | Microsoft.Search/searchServices/start/action | Avvia il servizio di ricerca. |
> | Azione | Microsoft.Search/searchServices/stop/action | Arresta il servizio di ricerca. |
> | Azione | Microsoft.Search/searchServices/write | Crea o aggiorna il servizio di ricerca. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Applica le regole di protezione avanzata del traffico specificate creando regole di sicurezza corrispondenti nei gruppi di sicurezza di rete specificati |
> | Azione | Microsoft.Security/adaptiveNetworkHardenings/read | Ottiene le raccomandazioni per la protezione avanzata della rete adattiva di una risorsa protetta di Azure |
> | Azione | Microsoft.Security/advancedThreatProtectionSettings/read | Ottiene le impostazioni di Advanced Threat Protection per la risorsa |
> | Azione | Microsoft.Security/advancedThreatProtectionSettings/write | Aggiorna le impostazioni di Advanced Threat Protection per la risorsa |
> | Azione | Microsoft.Security/alerts/read | Ottiene tutti gli avvisi di sicurezza disponibili |
> | Azione | Microsoft.Security/applicationWhitelistings/read | Ottiene gli elenchi elementi consentiti delle applicazioni |
> | Azione | Microsoft.Security/applicationWhitelistings/write | Crea un nuovo elenco elementi consentiti per l'applicazione o ne aggiorna uno esistente |
> | Azione | Microsoft. Security/assessmentMetadata/Read | Ottenere i metadati di valutazione della sicurezza disponibili nella sottoscrizione |
> | Azione | Microsoft. Security/assessmentMetadata/Write | Creare o aggiornare i metadati della valutazione della sicurezza |
> | Azione | Microsoft. Security/assessments/lettura | Ottenere valutazioni della sicurezza per la sottoscrizione |
> | Azione | Microsoft. Security/Assessment/scrittura | Creare o aggiornare valutazioni della sicurezza per la sottoscrizione |
> | Azione | Microsoft.Security/complianceResults/read | Ottiene i risultati di conformità per la risorsa |
> | Azione | Microsoft.Security/informationProtectionPolicies/read | Recupera le informazioni dei criteri di protezione per la risorsa |
> | Azione | Microsoft.Security/informationProtectionPolicies/write | Aggiorna le informazioni dei criteri di protezione per la risorsa |
> | Azione | Microsoft.Security/locations/alerts/activate/action | Attiva un avviso di sicurezza |
> | Azione | Microsoft.Security/locations/alerts/dismiss/action | Ignora un avviso di sicurezza |
> | Azione | Microsoft.Security/locations/alerts/read | Ottiene tutti gli avvisi di sicurezza disponibili |
> | Azione | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Elimina i criteri di accesso alla rete JIT |
> | Azione | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Avvia una richiesta di criteri di accesso alla rete JIT |
> | Azione | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Ottiene i criteri di accesso alla rete JIT |
> | Azione | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Crea un nuovo criterio di accesso alla rete JIT o ne aggiorna uno esistente |
> | Azione | Microsoft.Security/locations/read | Ottiene la posizione dei dati di sicurezza |
> | Azione | Microsoft.Security/locations/tasks/activate/action | Attiva un consiglio per la sicurezza |
> | Azione | Microsoft.Security/locations/tasks/dismiss/action | Ignora un consiglio per la sicurezza |
> | Azione | Microsoft.Security/locations/tasks/read | Ottiene tutti i consigli per la sicurezza disponibili |
> | Azione | Microsoft.Security/locations/tasks/resolve/action | Risolve un consiglio di sicurezza |
> | Azione | Microsoft.Security/locations/tasks/start/action | Avvia una raccomandazione di sicurezza |
> | Azione | Microsoft.Security/policies/read | Ottiene i criteri di sicurezza |
> | Azione | Microsoft.Security/policies/write | Aggiorna i criteri di sicurezza |
> | Azione | Microsoft.Security/pricings/delete | Elimina le informazioni sui prezzi per l'ambito |
> | Azione | Microsoft.Security/pricings/read | Ottiene le informazioni sui prezzi per l'ambito |
> | Azione | Microsoft.Security/pricings/write | Aggiorna le informazioni sui prezzi per l'ambito |
> | Azione | Microsoft.Security/register/action | Registra la sottoscrizione per Centro sicurezza di Azure |
> | Azione | Microsoft.Security/securityContacts/delete | Elimina il contatto di sicurezza |
> | Azione | Microsoft.Security/securityContacts/read | Ottiene il contatto di sicurezza |
> | Azione | Microsoft.Security/securityContacts/write | Aggiorna il contatto di sicurezza |
> | Azione | Microsoft.Security/securitySolutions/delete | Elimina una soluzione di sicurezza |
> | Azione | Microsoft.Security/securitySolutions/read | Ottiene le soluzioni di sicurezza |
> | Azione | Microsoft.Security/securitySolutions/write | Crea una nuova soluzione di sicurezza o ne aggiorna una esistente |
> | Azione | Microsoft.Security/securitySolutionsReferenceData/read | Ottiene i dati di riferimento delle soluzioni di sicurezza |
> | Azione | Microsoft.Security/securityStatuses/read | Ottiene gli stati di integrità della sicurezza per le soluzioni Azure |
> | Azione | Microsoft.Security/securityStatusesSummaries/read | Ottiene i riepiloghi degli stati di sicurezza per l'ambito |
> | Azione | Microsoft.Security/settings/read | Ottiene le impostazioni per l'ambito |
> | Azione | Microsoft.Security/settings/write | Aggiorna le impostazioni per l'ambito |
> | Azione | Microsoft.Security/tasks/read | Ottiene tutti i consigli per la sicurezza disponibili |
> | Azione | Microsoft.Security/unregister/action | Annulla la registrazione della sottoscrizione da Centro sicurezza di Azure |
> | Azione | Microsoft.Security/webApplicationFirewalls/delete | Elimina un firewall dell'applicazione Web |
> | Azione | Microsoft.Security/webApplicationFirewalls/read | Ottiene i firewall dell'applicazione Web |
> | Azione | Microsoft.Security/webApplicationFirewalls/write | Crea un nuovo firewall dell'applicazione Web o ne aggiorna uno esistente |
> | Azione | Microsoft.Security/workspaceSettings/connect/action | Modifica le impostazioni di riconnessione delle impostazioni dell'area di lavoro |
> | Azione | Microsoft.Security/workspaceSettings/delete | Elimina le impostazioni dell'area di lavoro |
> | Azione | Microsoft.Security/workspaceSettings/read | Ottiene le impostazioni dell'area di lavoro |
> | Azione | Microsoft.Security/workspaceSettings/write | Aggiorna le impostazioni dell'area di lavoro |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.SecurityGraph/diagnosticsettings/delete | Eliminazione di un'impostazione di diagnostica |
> | Azione | Microsoft.SecurityGraph/diagnosticsettings/read | Lettura di un'impostazione di diagnostica |
> | Azione | Microsoft.SecurityGraph/diagnosticsettings/write | Scrittura di un'impostazione di diagnostica |
> | Azione | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Lettura delle categorie di un'impostazione di diagnostica |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. SecurityInsights/Aggregations/Read | Ottiene informazioni aggregate |
> | Azione | Microsoft. SecurityInsights/alertRules/actions/Delete | Elimina le azioni di risposta di una regola di avviso |
> | Azione | Microsoft. SecurityInsights/alertRules/actions/Read | Ottiene le azioni di risposta di una regola di avviso |
> | Azione | Microsoft. SecurityInsights/alertRules/actions/Write | Aggiorna le azioni di risposta di una regola di avviso |
> | Azione | Microsoft. SecurityInsights/alertRules/Delete | Elimina le regole di avviso |
> | Azione | Microsoft. SecurityInsights/alertRules/Read | Ottiene le regole di avviso |
> | Azione | Microsoft. SecurityInsights/alertRules/Write | Aggiorna regole di avviso |
> | Azione | Microsoft. SecurityInsights/segnalibri/Delete | Elimina i segnalibri |
> | Azione | Microsoft. SecurityInsights/segnalibri/Espandi/azione | Ottiene le entità correlate di un'entità da un'espansione specifica |
> | Azione | Microsoft. SecurityInsights/segnalibri/lettura | Ottiene i segnalibri |
> | Azione | Microsoft. SecurityInsights/segnalibri/scrittura | Aggiorna segnalibri |
> | Azione | Microsoft. SecurityInsights/Cases/Comments/Read | Ottiene i commenti del case |
> | Azione | Microsoft. SecurityInsights/Cases/comments/Write | Crea i commenti del case |
> | Azione | Microsoft. SecurityInsights/Cases/Delete | Elimina un case |
> | Azione | Microsoft. SecurityInsights/Cases/Investigations/Read | Ottiene le analisi del case |
> | Azione | Microsoft. SecurityInsights/Cases/Investigations/Write | Aggiorna i metadati di un case |
> | Azione | Microsoft. SecurityInsights/Cases/Read | Ottiene un case |
> | Azione | Microsoft. SecurityInsights/Cases/Write | Aggiorna un case |
> | Azione | Microsoft. SecurityInsights/dataconnectors/Delete | Elimina un connettore dati |
> | Azione | Microsoft. SecurityInsights/dataconnectors/lettura | Ottiene i connettori dati |
> | Azione | Microsoft. SecurityInsights/dataconnectors/scrittura | Aggiorna un connettore dati |
> | Azione | Microsoft. SecurityInsights/Register/Action | Registra la sottoscrizione ad Azure Sentinel |
> | Azione | Microsoft. SecurityInsights/Settings/Read | Ottiene le impostazioni |
> | Azione | Microsoft. SecurityInsights/Settings/Write | Impostazioni relative agli aggiornamenti |
> | Azione | Microsoft. SecurityInsights/Annulla registrazione/azione | Annulla la registrazione della sottoscrizione da Azure Sentinel |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ServiceBus/checkNameAvailability/action | Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. |
> | Azione | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. Questa API è deprecata, usare invece CheckNameAvailability. |
> | Azione | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Elimina le regole della rete virtuale nel provider di risorse ServiceBus per la rete virtuale specificata |
> | Azione | Microsoft.ServiceBus/namespaces/authorizationRules/action | Aggiorna la regola di autorizzazione dello spazio dei nomi. Questa API è deprecata. Usare invece una chiamata PUT per aggiornare la regola di autorizzazione dello spazio dei nomi... Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita.  |
> | Azione | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Ottiene la stringa di connessione per lo spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/authorizationRules/read | Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi. |
> | Azione | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.ServiceBus/namespaces/authorizationRules/write | Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.ServiceBus/namespaces/Delete | Elimina la risorsa spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Ottiene le chiavi delle regole di autorizzazione per lo spazio dei nomi primario di ripristino di emergenza |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Ottiene le regole di autorizzazione dello spazio dei nomi primario di ripristino di emergenza |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Disabilita il ripristino di emergenza e arresta la replica delle modifiche dagli spazi dei nomi primari a quelli secondari. |
> | Azione | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica la disponibilità di alias dello spazio dei nomi nella sottoscrizione specificata. |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Elimina la configurazione di ripristino di emergenza associata allo spazio dei nomi. Questa operazione può essere chiamata solo tramite lo spazio dei nomi primario. |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Chiama il failover di ripristino di emergenza con ridondanza geografica e riconfigura l'alias dello spazio dei nomi affinché punti allo spazio dei nomi secondario. |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Recupera la configurazione di ripristino di emergenza associata allo spazio dei nomi. |
> | Azione | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Crea o aggiorna la configurazione di ripristino di emergenza associata allo spazio dei nomi. |
> | Azione | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Elimina il filtro di Griglia di eventi associato allo spazio dei nomi. |
> | Azione | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Ottiene il filtro di Griglia di eventi associato allo spazio dei nomi. |
> | Azione | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Crea o aggiorna il filtro di Griglia di eventi associato allo spazio dei nomi. |
> | Azione | Microsoft.ServiceBus/namespaces/eventhubs/read | Ottiene l'elenco delle descrizioni delle risorse dell'hub eventi |
> | Azione | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Elimina la risorsa filtro IP |
> | Azione | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Recupera la risorsa filtro IP |
> | Azione | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Crea la risorsa filtro IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Ricevere messaggi |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Inviare messaggi |
> | Azione | Microsoft.ServiceBus/namespaces/messagingPlan/read | Ottiene il piano di messaggistica per uno spazio dei nomi.<br>Questa API è deprecata.<br>Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive.<br>Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.ServiceBus/namespaces/messagingPlan/write | Aggiorna il piano di messaggistica per uno spazio dei nomi.<br>Questa API è deprecata.<br>Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive.<br>Questa operazione non è supportata per l'API versione 2017-04-01. |
> | Azione | Microsoft.ServiceBus/namespaces/migrate/action | Operazione di migrazione dello spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Elimina la configurazione della migrazione. |
> | Azione | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Recupera la configurazione della migrazione che restituisce lo stato della migrazione e delle operazioni di replica in sospeso |
> | Azione | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Annulla la migrazione dallo spazio dei nomi Standard a quello Premium |
> | Azione | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Assegna il DNS associato allo spazio dei nomi Standard allo spazio dei nomi Premium completando così la migrazione e arrestando la sincronizzazione delle risorse dallo spazio dei nomi Standard a quello Premium |
> | Azione | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Crea o aggiorna la configurazione di migrazione. Verrà avviata la sincronizzazione delle risorse dallo spazio dei nomi Standard a quello Premium |
> | Azione | Microsoft. ServiceBus/Namespaces/networkruleset/Delete | Elimina la risorsa regola di rete virtuale |
> | Azione | Microsoft. ServiceBus/Namespaces/networkruleset/Read | Ottiene la risorsa NetworkRuleSet |
> | Azione | Microsoft. ServiceBus/Namespaces/networkruleset/Write | Crea risorsa regola di rete virtuale |
> | Azione | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Elimina la risorsa regola di rete virtuale |
> | Azione | Microsoft.ServiceBus/namespaces/networkrulesets/read | Ottiene la risorsa NetworkRuleSet |
> | Azione | Microsoft.ServiceBus/namespaces/networkrulesets/write | Crea risorsa regola di rete virtuale |
> | Azione | Microsoft.ServiceBus/namespaces/operationresults/read | Operazione di recupero dello stato dello spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operazione per aggiornare la coda. Questa operazione non è supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione. |
> | Azione | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operazione per l'eliminazione di regole di autorizzazione delle code |
> | Azione | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Ottiene la stringa di connessione alla coda |
> | Azione | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Ottiene l'elenco delle regole di autorizzazione delle code |
> | Azione | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Crea regole di autorizzazione delle code e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.ServiceBus/namespaces/queues/Delete | Operazione per l'eliminazione delle risorse coda |
> | Azione | Microsoft.ServiceBus/namespaces/queues/read | Ottiene l'elenco delle descrizioni delle risorse coda |
> | Azione | Microsoft.ServiceBus/namespaces/queues/write | Crea o aggiorna proprietà di coda. |
> | Azione | Microsoft.ServiceBus/namespaces/read | Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi |
> | Azione | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Rimuove uno spazio dei nomi ACS |
> | Azione | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operazione per aggiornare l'argomento. Questa operazione non è supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione. |
> | Azione | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operazione per l'eliminazione di regole di autorizzazione dell’argomento |
> | Azione | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Ottiene la stringa di connessione all’argomento |
> | Azione | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Ottiene l'elenco delle regole di autorizzazione dell’argomento |
> | Azione | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Rigenera la chiave primaria o secondaria per la risorsa |
> | Azione | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Crea regole di autorizzazione dell’argomento e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione. |
> | Azione | Microsoft.ServiceBus/namespaces/topics/Delete | Operazione per l'eliminazione di risorse argomento |
> | Azione | Microsoft.ServiceBus/namespaces/topics/read | Ottiene l'elenco delle descrizioni delle risorse argomento |
> | Azione | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operazione per l’eliminazione di una risorsa TopicSubscription |
> | Azione | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Ottiene l'elenco delle descrizioni delle risorse TopicSubscription |
> | Azione | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operazione per l'eliminazione delle risorse di una regola |
> | Azione | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Ottiene l'elenco delle descrizioni delle risorse di una regola |
> | Azione | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Crea o aggiorna le proprietà di una regola. |
> | Azione | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Crea o aggiorna le proprietà TopicSubscription. |
> | Azione | Microsoft.ServiceBus/namespaces/topics/write | Crea o aggiorna le proprietà degli argomenti. |
> | Azione | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Elimina la risorsa regola di rete virtuale |
> | Azione | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Recupera la risorsa regola di rete virtuale |
> | Azione | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Crea risorsa regola di rete virtuale |
> | Azione | Microsoft.ServiceBus/namespaces/write | Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi. |
> | Azione | Microsoft.ServiceBus/operations/read | Ottiene operazioni |
> | Azione | Microsoft.ServiceBus/register/action | Registra la sottoscrizione per il provider di risorse bus di servizio e consente la creazione di risorse bus di servizio |
> | Azione | Microsoft.ServiceBus/sku/read | Ottiene l'elenco delle descrizioni di una risorsa SKU |
> | Azione | Microsoft.ServiceBus/sku/regions/read | Ottiene l'elenco delle descrizioni di una risorsa SkuRegions |
> | Azione | Microsoft.ServiceBus/unregister/action | Registra la sottoscrizione per il provider di risorse bus di servizio e consente la creazione di risorse bus di servizio |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.ServiceFabric/clusters/applications/delete | Elimina tutte le applicazioni |
> | Azione | Microsoft.ServiceFabric/clusters/applications/read | Legge tutte le applicazioni |
> | Azione | Microsoft.ServiceFabric/clusters/applications/services/delete | Elimina tutti i servizi |
> | Azione | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Legge tutte le partizioni |
> | Azione | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Legge tutte le repliche |
> | Azione | Microsoft.ServiceFabric/clusters/applications/services/read | Legge tutti i servizi |
> | Azione | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Legge tutti gli stati di servizio |
> | Azione | Microsoft.ServiceFabric/clusters/applications/services/write | Crea o aggiorna tutti i servizi |
> | Azione | Microsoft.ServiceFabric/clusters/applications/write | Crea o aggiorna tutte le applicazioni |
> | Azione | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Elimina tutti i tipi di applicazioni |
> | Azione | Microsoft.ServiceFabric/clusters/applicationTypes/read | Legge tutti i tipi di applicazioni |
> | Azione | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Elimina tutte le versioni del tipo di applicazione |
> | Azione | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Legge tutte le versioni del tipo di applicazione |
> | Azione | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Crea o aggiorna tutte le versioni del tipo di applicazione |
> | Azione | Microsoft.ServiceFabric/clusters/applicationTypes/write | Crea o aggiorna tutti i tipi di applicazioni |
> | Azione | Microsoft.ServiceFabric/clusters/delete | Elimina tutti i cluster |
> | Azione | Microsoft.ServiceFabric/clusters/nodes/read | Legge tutti i nodi |
> | Azione | Microsoft.ServiceFabric/clusters/read | Legge tutti i cluster |
> | Azione | Microsoft.ServiceFabric/clusters/statuses/read | Legge tutti gli stati del cluster |
> | Azione | Microsoft.ServiceFabric/clusters/write | Crea o aggiorna tutti i cluster |
> | Azione | Microsoft.ServiceFabric/locations/clusterVersions/read | Legge tutte le versioni del cluster |
> | Azione | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Legge tutte le versioni cluster per un ambiente specifico |
> | Azione | Microsoft.ServiceFabric/locations/operationresults/read | Legge tutti i risultati delle operazioni |
> | Azione | Microsoft.ServiceFabric/locations/operations/read | Legge tutte le operazioni per località |
> | Azione | Microsoft.ServiceFabric/operations/read | Legge tutte le operazioni disponibili |
> | Azione | Microsoft.ServiceFabric/register/action | Registra tutte le azioni |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.SignalRService/locations/checknameavailability/action | Verifica se un nome è disponibile per essere assegnato a un nuovo servizio SignalR |
> | Azione | Microsoft.SignalRService/locations/operationresults/signalr/read | Eseguire una query sullo stato di un'operazione asincrona |
> | Azione | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Eseguire una query sullo stato di un'operazione asincrona |
> | Azione | Microsoft.SignalRService/locations/usages/read | Ottiene gli utilizzi della quota per il servizio Azure SignalR |
> | Azione | Microsoft.SignalRService/operationresults/read | Eseguire una query sullo stato di un'operazione asincrona |
> | Azione | Microsoft. SignalRService/Operations/Read | Elencare le operazioni per il servizio Azure SignalR. |
> | Azione | Microsoft.SignalRService/operationstatus/read | Eseguire una query sullo stato di un'operazione asincrona |
> | Azione | Microsoft.SignalRService/register/action | Registra il provider di risorse "Microsoft.SignalRService" con una sottoscrizione |
> | Azione | Microsoft.SignalRService/SignalR/delete | Elimina l'intero servizio SignalR |
> | Azione | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Elimina un filtro di griglia di eventi da un SignalR. |
> | Azione | Microsoft.SignalRService/SignalR/eventGridFilters/read | Ottiene le proprietà del filtro di griglia di eventi specificato o elenca tutti i filtri di griglia di eventi per il SignalR specificato. |
> | Azione | Microsoft.SignalRService/SignalR/eventGridFilters/write | Crea o aggiorna un filtro di griglia di eventi per un SignalR con i parametri specificati. |
> | Azione | Microsoft.SignalRService/SignalR/listkeys/action | Visualizza il valore delle chiavi di accesso di SignalR nel portale di gestione o tramite API |
> | Azione | Microsoft. SignalRService/SignalR/privateEndpointConnectionProxies/Delete | Eliminare un proxy di connessione all'endpoint privato |
> | Azione | Microsoft. SignalRService/SignalR/privateEndpointConnectionProxies/Read | Leggere un proxy allacciamento dell'endpoint privato |
> | Azione | Microsoft. SignalRService/SignalR/privateEndpointConnectionProxies/Validate/Action | Convalidare un proxy di connessione all'endpoint privato |
> | Azione | Microsoft. SignalRService/SignalR/privateEndpointConnectionProxies/Write | Creare un proxy di connessione all'endpoint privato |
> | Azione | Microsoft. SignalRService/SignalR/privateEndpointConnections/Read | Lettura di una connessione a un endpoint privato |
> | Azione | Microsoft. SignalRService/SignalR/privateEndpointConnections/Write | Approva o rifiuta una connessione all'endpoint privato |
> | Azione | Microsoft. SignalRService/SignalR/privateLinkResources/Read | Elencare tutte le risorse di collegamento privato SignalR |
> | Azione | Microsoft.SignalRService/SignalR/read | Visualizza le impostazioni e le configurazioni di SignalR nel portale di gestione o tramite API |
> | Azione | Microsoft.SignalRService/SignalR/regeneratekey/action | Modifica il valore delle chiavi di accesso di SignalR nel portale di gestione o tramite API |
> | Azione | Microsoft.SignalRService/SignalR/restart/action | Riavvia un servizio Azure SignalR nel portale di gestione o tramite API. Si verificherà un certo tempo di inattività. |
> | Azione | Microsoft.SignalRService/SignalR/write | Modifica le impostazioni e le configurazioni di SignalR nel portale di gestione o tramite API |
> | Azione | Microsoft.SignalRService/unregister/action | Annulla la registrazione del provider di risorse "Microsoft.SignalRService" con una sottoscrizione |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Elenca gli elementi dell'applicazione della definizione dell'applicazione. |
> | Azione | Microsoft.Solutions/applicationDefinitions/delete | Rimuove una definizione di applicazione. |
> | Azione | Microsoft.Solutions/applicationDefinitions/read | Recupera un elenco di definizioni di applicazione. |
> | Azione | Microsoft.Solutions/applicationDefinitions/write | Aggiunge o modifica una definizione di applicazione. |
> | Azione | Microsoft.Solutions/applications/applicationArtifacts/read | Elenca gli elementi dell'applicazione. |
> | Azione | Microsoft.Solutions/applications/delete | Rimuove un'applicazione. |
> | Azione | Microsoft.Solutions/applications/read | Recupera un elenco di applicazioni. |
> | Azione | Microsoft.Solutions/applications/refreshPermissions/action | Aggiorna le autorizzazioni dell'applicazione. |
> | Azione | Microsoft.Solutions/applications/updateAccess/action | Aggiorna l'accesso alle applicazioni. |
> | Azione | Microsoft.Solutions/applications/write | Crea un'applicazione. |
> | Azione | Microsoft.Solutions/jitRequests/delete | Rimuove una richiesta JIT |
> | Azione | Microsoft.Solutions/jitRequests/read | Recupera un elenco di richieste JIT |
> | Azione | Microsoft.Solutions/jitRequests/write | Crea una richiesta JIT |
> | Azione | Microsoft.Solutions/locations/operationStatuses/read | Legge lo stato dell'operazione per la risorsa. |
> | Azione | Microsoft. Solutions/Operations/Read | Recupera l'elenco delle operazioni. |
> | Azione | Microsoft.Solutions/register/action | Esegue la registrazione in Microsoft Solutions. |
> | Azione | Microsoft.Solutions/unregister/action | Annulla la registrazione dalle soluzioni. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Sql/checkNameAvailability/action | Verifica se un determinato nome server è disponibile per il provisioning in tutto il mondo per una determinata sottoscrizione. |
> | Azione | Microsoft.Sql/instancePools/delete | Elimina un pool di istanze |
> | Azione | Microsoft.Sql/instancePools/read | Ottiene un pool di istanze |
> | Azione | Microsoft.Sql/instancePools/usages/read | Ottiene le informazioni sull'utilizzo del pool di istanze |
> | Azione | Microsoft.Sql/instancePools/write | Crea o aggiorna un pool di istanze |
> | Azione | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Recupera il risultato dell'operazione di impostazione criteri di controllo BLOB del server esteso |
> | Azione | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Recupera il risultato dell’operazione di impostazione criteri di controllo BLOB del server |
> | Azione | Microsoft.Sql/locations/capabilities/read | Ottiene le capacitò per questa sottoscrizione in una determinata posizione |
> | Azione | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Ottiene lo stato di un'operazione del database. |
> | Azione | Microsoft.Sql/locations/databaseOperationResults/read | Ottiene lo stato di un'operazione del database. |
> | Azione | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Ottiene le operazioni in corso sul server eliminato |
> | Azione | Microsoft.Sql/locations/deletedServerOperationResults/read | Ottiene le operazioni in corso sul server eliminato |
> | Azione | Microsoft.Sql/locations/deletedServers/read | Restituisce l'elenco dei server eliminati o ottiene le proprietà per il server eliminato specificato. |
> | Azione | Microsoft.Sql/locations/deletedServers/recover/action | Recupera un server eliminato |
> | Azione | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Ottiene l'operazione asincrona di Azure per un'operazione asincrona di pool di database elastico |
> | Azione | Microsoft.Sql/locations/elasticPoolOperationResults/read | Ottiene i risultati di un'operazione di pool di database elastico. |
> | Azione | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Ottiene le operazioni in corso sulla protezione della crittografia Transparent Data Encryption |
> | Azione | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Ottiene le operazioni in corso sulla protezione della crittografia Transparent Data Encryption |
> | Azione | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Recupera il risultato dell'operazione di impostazione criteri di controllo BLOB del server esteso |
> | Azione | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Recupera il risultato dell'operazione di impostazione criteri di controllo BLOB del server esteso |
> | Azione | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Recupera lo stato di una regola del firewall. |
> | Azione | Microsoft.Sql/locations/firewallRulesOperationResults/read | Recupera lo stato di una regola del firewall. |
> | Azione | Microsoft.Sql/locations/instanceFailoverGroups/delete | Elimina un gruppo di failover dell'istanza esistente. |
> | Azione | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Esegue il failover pianificato in un gruppo di failover dell'istanza esistente. |
> | Azione | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Esegue il failover forzato in un gruppo di failover dell'istanza esistente. |
> | Azione | Microsoft.Sql/locations/instanceFailoverGroups/read | Restituisce l'elenco dei gruppi di failover dell'istanza o recupera le proprietà del gruppo di failover dell'istanza specificato. |
> | Azione | Microsoft.Sql/locations/instanceFailoverGroups/write | Crea un gruppo di failover dell'istanza con i parametri specificati o aggiorna le proprietà o i tag per il gruppo di failover dell'istanza specificato. |
> | Azione | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Ottiene lo stato di un'operazione del pool di istanze |
> | Azione | Microsoft.Sql/locations/instancePoolOperationResults/read | Ottiene il risultato per un'operazione del pool di istanze |
> | Azione | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Restituisce i dettagli di una specifica operazione asincrona di Azure sull'endpoint di interfaccia |
> | Azione | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Restituisce i dettagli di una specifica operazione di profilo sull'endpoint di interfaccia |
> | Azione | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Ottiene lo stato di un'operazione agente processo. |
> | Azione | Microsoft.Sql/locations/jobAgentOperationResults/read | Ottiene il risultato di un'operazione agente processo. |
> | Azione | Microsoft.Sql/locations/longTermRetentionBackups/read | Elenca i backup di conservazione a lungo termine per tutti i database in ogni server in una posizione |
> | Azione | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Elenca i backup di conservazione a lungo termine per tutti i database in un server |
> | Azione | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Elimina un backup di conservazione a lungo termine |
> | Azione | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Elenca i backup di conservazione a lungo termine per un database |
> | Azione | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Completa l'operazione di ripristino del database gestito |
> | Azione | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Ottiene le operazioni in corso sulla protezione di crittografia dell'istanza gestita Transparent Data Encryption |
> | Azione | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Ottiene le operazioni in corso sulla protezione di crittografia dell'istanza gestita Transparent Data Encryption |
> | Azione | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Ottiene le operazioni in corso sulle chiavi di istanza gestite Transparent Data Encryption |
> | Azione | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Ottiene le operazioni in corso sulle chiavi di istanza gestite Transparent Data Encryption |
> | Azione | Microsoft. SQL/locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/Read | Ottiene lo stato di un'operazione di criteri di conservazione a lungo termine per un database gestito |
> | Azione | Microsoft. SQL/locations/managedInstanceLongTermRetentionPolicyOperationResults/Read | Ottiene lo stato di un'operazione di criteri di conservazione a lungo termine per un database gestito |
> | Azione | Microsoft. SQL/locations/managedShortTermRetentionPolicyOperationResults/Read | Ottiene lo stato di un'operazione di criteri di conservazione a breve termine |
> | Azione | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Ottiene le operazioni in corso sulla crittografia trasparente dei dati del database gestito |
> | Azione | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Ottiene le operazioni in corso sulla crittografia trasparente dei dati del database gestito |
> | Azione | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Ottiene il risultato per un'operazione di connessione di un endpoint privato |
> | Azione | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Ottiene il risultato per un'operazione proxy di connessione all'endpoint privato |
> | Azione | Microsoft.Sql/locations/read | Ottiene le posizioni disponibili per una determinata sottoscrizione |
> | Azione | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft.Sql/locations/serverKeyOperationResults/read | Ottiene le operazioni in corso sulle chiavi del server Transparent Data Encryption |
> | Azione | Microsoft. SQL/locations/shortTermRetentionPolicyOperationResults/Read | Ottiene lo stato di un'operazione di criteri di conservazione a breve termine |
> | Azione | Microsoft.Sql/locations/syncAgentOperationResults/read | Recupera i risultati di un'operazione sulla risorsa dell'agente di sincronizzazione |
> | Azione | Microsoft.Sql/locations/syncDatabaseIds/read | Recupera gli ID del database di sincronizzazione per una particolare area e sottoscrizione |
> | Azione | Microsoft.Sql/locations/syncGroupOperationResults/read | Recupera i risultati di un'operazione sulla risorsa del gruppo di sincronizzazione |
> | Azione | Microsoft.Sql/locations/syncMemberOperationResults/read | Recupera i risultati di un'operazione sulla risorsa del membro di sincronizzazione |
> | Azione | Microsoft.Sql/locations/usages/read | Ottiene una raccolta di metrica di utilizzo per questa sottoscrizione in una posizione |
> | Azione | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Restituisce i dettagli della specifica operazione asincrona di Azure per le regole di rete virtuale  |
> | Azione | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Restituisce i dettagli della specifica operazione con le regole di rete virtuale  |
> | Azione | Microsoft.Sql/managedInstances/administrators/delete | Elimina un amministratore esistente dell'istanza gestita. |
> | Azione | Microsoft.Sql/managedInstances/administrators/read | Ottiene un elenco degli amministratori di istanza gestita. |
> | Azione | Microsoft.Sql/managedInstances/administrators/write | Crea o aggiorna un amministratore di istanza gestita con i parametri specificati. |
> | Azione | Microsoft. SQL/managedInstances/databases/backupLongTermRetentionPolicies/Read | Ottiene un criterio di conservazione a lungo termine per un database gestito |
> | Azione | Microsoft. SQL/managedInstances/databases/backupLongTermRetentionPolicies/Write | Aggiorna i criteri di conservazione a lungo termine per un database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Ottiene i criteri di conservazione a breve termine per un database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Aggiorna i criteri di conservazione a breve termine per un database gestito |
> | Azione | Microsoft. SQL/managedInstances/database/colonne/lettura | Restituisce un elenco di colonne per un database gestito |
> | Azione | Microsoft. SQL/managedInstances/databases/completeRestore/azione | Completa l'operazione di ripristino del database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Elenca le etichette di riservatezza di un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Etichette di riservatezza aggiornamenti batch |
> | Azione | Microsoft.Sql/managedInstances/databases/delete | Elimina un database gestito esistente |
> | Azione | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Ottiene i log disponibili per i database dell'istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/databases/read | Ottiene un database gestito esistente |
> | Azione | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Elenca le etichette di riservatezza di un determinato database |
> | Azione | Microsoft. SQL/managedInstances/databases/recommendedSensitivityLabels/Write | Etichette di riservatezza consigliate per l'aggiornamento batch |
> | Azione | Microsoft. SQL/managedInstances/databases/restoreDetails/Read | Restituisce i dettagli del ripristino del database gestito mentre è in corso il ripristino. |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/read | Ottenere uno schema di database gestito. |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Ottenere una colonna del database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Elimina l'etichetta di riservatezza di una determinata colonna |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Disabilitare le raccomandazioni di riservatezza in una colonna specifica |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Abilita raccomandazioni di riservatezza su una determinata colonna |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Ottiene l'etichetta di riservatezza di una determinata colonna |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Crea o aggiorna l'etichetta di riservatezza di una determinata colonna |
> | Azione | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Ottenere una tabella di database gestita |
> | Azione | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Recupera un elenco di criteri di rilevamento delle minacce del database gestito configurati per un determinato server |
> | Azione | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce per uno specifico database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/securityEvents/read | Recupera gli eventi di sicurezza del database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Elenca le etichette di riservatezza di un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Recupera i dettagli di Transparent Data Encryption per uno specifico database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Modifica Transparent Data Encryption per uno specifico database gestito |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Rimuove la valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Recupera i criteri di valutazione della vulnerabilità in un database specifico |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Rimuove la baseline della regola di valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Ottiene la baseline della regola di valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Modifica la baseline della regola di valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converte un risultato di analisi esistente in un formato leggibile. Se esiste già non accade nulla |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Esegue la scansione del database per la valutazione della vulnerabilità. |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Restituisce l'elenco dei record dell'analisi di valutazione delle vulnerabilità del database o ottiene il record di analisi per l'ID analisi specificato. |
> | Azione | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Modifica la valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/managedInstances/databases/write | Crea un nuovo database o ne aggiorna uno esistente. |
> | Azione | Microsoft.Sql/managedInstances/delete | Elimina un'istanza gestita esistente. |
> | Azione | Microsoft.Sql/managedInstances/encryptionProtector/read | Ottiene un elenco delle protezioni di crittografia dei server o ottiene le proprietà per la protezione di crittografia del server specificato. |
> | Azione | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | Aggiorna le proprietà per la protezione di crittografia del server specificato. |
> | Azione | Microsoft.Sql/managedInstances/encryptionProtector/write | Aggiorna le proprietà per la protezione di crittografia del server specificato. |
> | Azione | Microsoft. SQL/managedInstances/inaccessibleManagedDatabases/Read | Ottiene un elenco di database gestiti inaccessibili in un'istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/keys/delete | Elimina la chiave di un'istanza gestita di SQL di Azure esistente. |
> | Azione | Microsoft.Sql/managedInstances/keys/read | Restituisce l'elenco delle chiavi delle istanze gestite o ottiene le proprietà della chiave dell'istanza gestita specificata. |
> | Azione | Microsoft.Sql/managedInstances/keys/write | Crea una chiave con i parametri specificati o aggiorna le proprietà o i tag per la chiave dell'istanza gestita specificata. |
> | Azione | Microsoft.Sql/managedInstances/metricDefinitions/read | Ottiene le definizioni della metrica dell'istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/metrics/read | Ottiene la metrica dell'istanza gestita |
> | Azione | Microsoft. SQL/managedInstances/Operations/Annulla/azione | Annulla l'operazione asincrona in sospeso Istanza gestita SQL di Azure che non è ancora stata completata. |
> | Azione | Microsoft. SQL/managedInstances/Operations/Read | Ottenere le operazioni dell'istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Ottiene i log disponibili per le istanze gestite |
> | Azione | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metrica disponibili per le istanze gestite |
> | Azione | Microsoft.Sql/managedInstances/read | Restituisce l'elenco delle istanze gestite o ottiene le proprietà per l'istanza gestita specificata. |
> | Azione | Microsoft.Sql/managedInstances/recoverableDatabases/read | Restituisce un elenco di database gestiti recuperabili |
> | Azione | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Ottiene i criteri di conservazione a breve termine per un database gestito rilasciato |
> | Azione | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Aggiorna i criteri di conservazione a breve termine per un database gestito rilasciato |
> | Azione | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Restituisce un elenco di database gestiti eliminati ripristinabili. |
> | Azione | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Recupera un elenco di criteri di rilevamento minacce del server gestito configurati per un determinato server |
> | Azione | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce per uno specifico server gestito |
> | Azione | Microsoft.Sql/managedInstances/tdeCertificates/action | Crea/aggiorna un certificato TDE |
> | Azione | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Rimuove la valutazione delle vulnerabilità per una determinata istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Recupera i criteri di valutazione della vulnerabilità in un’istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Cambia la valutazione delle vulnerabilità per una determinata istanza gestita |
> | Azione | Microsoft.Sql/managedInstances/write | Crea un'istanza gestita con i parametri specificati o aggiorna le proprietà o i tag per l'istanza gestita specificata. |
> | Azione | Microsoft.Sql/operations/read | Ottiene le operazioni REST disponibili |
> | Azione | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Determina se l'utente può approvare una connessione all'endpoint privato |
> | Azione | Microsoft.Sql/register/action | Registra la sottoscrizione per il provider di risorse del database Microsoft SQL e consente la creazione di database Microsoft SQL. |
> | Azione | Microsoft.Sql/servers/administrators/delete | Elimina un oggetto Azure Active Directory amministratore specifico |
> | Azione | Microsoft.Sql/servers/administrators/read | Ottiene un oggetto Azure Active Directory amministratore specifico |
> | Azione | Microsoft.Sql/servers/administrators/write | Aggiunge o aggiorna un oggetto Azure Active Directory amministratore specifico |
> | Azione | Microsoft.Sql/servers/advisors/read | Restituisce l'elenco degli advisor disponibili per il server |
> | Azione | Microsoft.Sql/servers/advisors/recommendedActions/read | Restituisce l'elenco di azioni consigliate per l’advisor specificato per il server |
> | Azione | Microsoft.Sql/servers/advisors/recommendedActions/write | Applica l'azione consigliata al server |
> | Azione | Microsoft.Sql/servers/advisors/write | Aggiorna lo stato di esecuzione automatica di un advisor a livello di server. |
> | Azione | Microsoft.Sql/servers/auditingPolicies/read | Recupera i dettagli dei criteri di controllo della tabella del server predefinito configurati in uno specifico database |
> | Azione | Microsoft.Sql/servers/auditingPolicies/write | Modifica il controllo della tabella del server predefinito per uno specifico server |
> | Azione | Microsoft.Sql/servers/auditingSettings/operationResults/read | Recupera il risultato dell’operazione di impostazione criteri di controllo BLOB del server |
> | Azione | Microsoft.Sql/servers/auditingSettings/read | Recupera i dettagli dei criteri di controllo del BLOB del server configurati in uno specifico server |
> | Azione | Microsoft.Sql/servers/auditingSettings/write | Modifica il controllo del BLOB del server per uno specifico server |
> | Azione | Microsoft.Sql/servers/automaticTuning/read | Restituisce le impostazioni di ottimizzazione automatica per il server |
> | Azione | Microsoft.Sql/servers/automaticTuning/write | Aggiorna le impostazioni di ottimizzazione automatica per il server e restituisce le impostazioni aggiornate |
> | Azione | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Elimina un insieme di credenziali di archiviazione per un backup esistente. |
> | Azione | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Questa operazione serve per acquisire un insieme di credenziali di conservazione a lungo termine del backup. Restituisce informazioni sull'insieme di credenziali registrato su questo server |
> | Azione | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Questa operazione serve per registrare un insieme di credenziali di backup con conservazione a lungo termine in un server |
> | Azione | Microsoft.Sql/servers/communicationLinks/delete | Elimina un collegamento di comunicazione server esistente. |
> | Azione | Microsoft.Sql/servers/communicationLinks/read | Restituisce l'elenco dei collegamenti di comunicazione di un server specificato. |
> | Azione | Microsoft.Sql/servers/communicationLinks/write | Crea o aggiorna un collegamento di comunicazione del server. |
> | Azione | Microsoft.Sql/servers/connectionPolicies/read | Restituisce l'elenco dei criteri di connessione del server per un server specificato. |
> | Azione | Microsoft.Sql/servers/connectionPolicies/write | Crea o aggiorna i criteri di connessione del server. |
> | Azione | Microsoft.Sql/servers/databases/advisors/read | Restituisce l'elenco degli advisor disponibili per il database |
> | Azione | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Restituisce l'elenco di azioni consigliate per l’advisor specificato per il database |
> | Azione | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Applica l'azione consigliata al database |
> | Azione | Microsoft.Sql/servers/databases/advisors/write | Aggiorna lo stato di esecuzione automatica di un advisor a livello di database. |
> | Azione | Microsoft.Sql/servers/databases/auditingPolicies/read | Recupera i dettagli dei criteri di controllo della tabella configurati in uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/auditingPolicies/write | Modifica i criteri di controllo della tabella per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/auditingSettings/read | Recupera i dettagli dei criteri di controllo del BLOB configurati in uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/auditingSettings/write | Modifica i criteri di controllo del BLOB per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
> | Azione | Microsoft.Sql/servers/databases/automaticTuning/read | Restituisce le impostazioni di ottimizzazione automatica per un database |
> | Azione | Microsoft.Sql/servers/databases/automaticTuning/write | Aggiorna le impostazioni di ottimizzazione automatica per un database e restituisce le impostazioni aggiornate |
> | Azione | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Ottiene lo stato di un'operazione del database. |
> | Azione | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Restituisce l'elenco dei criteri di archiviazione di backup di un database specifico. |
> | Azione | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Crea o aggiorna i criteri di archiviazione di backup di un database. |
> | Azione | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Ottiene un criterio di conservazione a breve termine per un database |
> | Azione | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Aggiorna un criterio di conservazione a breve termine per un database |
> | Azione | Microsoft. SQL/Servers/databases/Columns/lettura | Restituisce un elenco di colonne per un database |
> | Azione | Microsoft.Sql/servers/databases/connectionPolicies/read | Recupera i dettagli dei criteri di connessione configurati su uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/connectionPolicies/write | Modifica i criteri di connessione per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Elenca le etichette di riservatezza di un determinato database |
> | Azione | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Etichette di riservatezza aggiornamenti batch |
> | Azione | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Restituisce l'elenco dei criteri di maschera dati del database. |
> | Azione | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Elimina la regola per i criteri di mascheramento dati per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Recupera i dettagli della regola per i criteri di mascheramento dati configurati in uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Modifica la regola per i criteri di mascheramento dati per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Modifica i criteri di mascheramento dati per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Restituisce le informazioni sul passaggio della query distribuita della query del data warehouse per l’ID passaggio selezionato |
> | Azione | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Restituisce le informazioni sulla query di distribuzione del data warehouse per l’ID query selezionato |
> | Azione | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Recupera le attività dell'utente per un'istanza di SQL Data Warehouse incluse le query in esecuzione e sospese |
> | Azione | Microsoft.Sql/servers/databases/delete | Elimina un database esistente. |
> | Azione | Microsoft.Sql/servers/databases/export/action | Esporta un database SQL di Azure |
> | Azione | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB esteso configurati in uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Modifica i criteri di controllo BLOB esteso per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/extensions/read | Ottiene una raccolta di estensioni per il database. |
> | Azione | Microsoft.Sql/servers/databases/extensions/write | Modifica l'estensione per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/failover/action | Failover del database avviato dal cliente. |
> | Azione | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Recupera i criteri di backup di replica geografica per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Crea o aggiorna i criteri di backup di replica geografica di un database |
> | Azione | Microsoft.Sql/servers/databases/importExportOperationResults/read | Ottiene le operazioni di importazione/esportazione in corso |
> | Azione | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Ottiene un elenco delle finestre di manutenzione disponibili per un database selezionato. |
> | Azione | Microsoft.Sql/servers/databases/maintenanceWindows/read | Ottiene le impostazioni delle finestre di manutenzione per un database selezionato. |
> | Azione | Microsoft.Sql/servers/databases/maintenanceWindows/write | Configura le impostazioni delle finestre di manutenzione per un database selezionato. |
> | Azione | Microsoft.Sql/servers/databases/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i database |
> | Azione | Microsoft.Sql/servers/databases/metrics/read | Restituisce la metrica per i database |
> | Azione | Microsoft.Sql/servers/databases/move/action | Modificare il nome di un database esistente. |
> | Azione | Microsoft.Sql/servers/databases/operationResults/read | Ottiene lo stato di un'operazione del database. |
> | Azione | Microsoft.Sql/servers/databases/operations/cancel/action | Annulla l'operazione asincrona in sospeso del database SQL di Azure che non è ancora stata completata. |
> | Azione | Microsoft.Sql/servers/databases/operations/read | Restituisce l'elenco delle operazioni eseguite nel database |
> | Azione | Microsoft.Sql/servers/databases/pause/action | Mette in pausa il database Azure SQL Datawarehouse |
> | Azione | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Ottiene i log disponibili per i database |
> | Azione | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i database |
> | Azione | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Restituisce la raccolta dei testi delle query che corrispondono ai parametri specificati. |
> | Azione | Microsoft.Sql/servers/databases/queryStore/read | Restituisce i valori correnti delle impostazioni Query Store per il database. |
> | Azione | Microsoft.Sql/servers/databases/queryStore/write | Aggiorna le impostazioni di Archivio query per il database |
> | Azione | Microsoft.Sql/servers/databases/read | Restituisce l'elenco dei database o ottiene le proprietà per il database specificato. |
> | Azione | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Elenca le etichette di riservatezza di un determinato database |
> | Azione | Microsoft. SQL/Servers/databases/recommendedSensitivityLabels/Write | Etichette di riservatezza consigliate per l'aggiornamento batch |
> | Azione | Microsoft.Sql/servers/databases/replicationLinks/delete | Termina forzatamente la relazione di replica e con potenziale perdita di dati |
> | Azione | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Failover dopo la sincronizzazione di tutte modifiche dal database primario, rendendo questo database primario nella relazione di replica e rendendo il database primario remoto il database secondario |
> | Azione | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Esegue immediatamente il failover con potenziale perdita di dati, rendendo questo database primario nella relazione di replica e rendendo il database primario remoto il database secondario |
> | Azione | Microsoft.Sql/servers/databases/replicationLinks/read | Restituisce l'elenco dei collegamenti di replica o ottiene le proprietà dei collegamenti di replica specificati. |
> | Azione | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Termina forzatamente la relazione di replica o dopo la sincronizzazione con il partner |
> | Azione | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Aggiorna la modalità di replica per il collegamento in modalità sincrona o asincrona |
> | Azione | Microsoft.Sql/servers/databases/restorePoints/action | Crea un nuovo punto di ripristino |
> | Azione | Microsoft.Sql/servers/databases/restorePoints/delete | Elimina un punto di ripristino per il database. |
> | Azione | Microsoft.Sql/servers/databases/restorePoints/read | Restituisce i punti di ripristino per il database. |
> | Azione | Microsoft.Sql/servers/databases/resume/action | Riprende l'esecuzione del database Azure SQL Datawarehouse |
> | Azione | Microsoft.Sql/servers/databases/schemas/read | Ottenere uno schema di database. |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Ottenere una colonna di database. |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Elimina l'etichetta di riservatezza di una determinata colonna |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Disabilitare le raccomandazioni di riservatezza in una colonna specifica |
> | Azione | Microsoft. SQL/Servers/databases/schemas/Tables/Columns/sensitivityLabels/Enable/Action | Abilita raccomandazioni di riservatezza su una determinata colonna |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Ottiene l'etichetta di riservatezza di una determinata colonna |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Crea o aggiorna l'etichetta di riservatezza di una determinata colonna |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/read | Ottenere una tabella di database. |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Recupera l’elenco di raccomandazioni sull’indice su un database |
> | Azione | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Applica la raccomandazione sull’indice |
> | Azione | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Recupera un elenco di criteri di rilevamento minacce del database configurati per un determinato server |
> | Azione | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/securityMetrics/read | Ottiene una raccolta della metrica di sicurezza del database |
> | Azione | Microsoft.Sql/servers/databases/sensitivityLabels/read | Elenca le etichette di riservatezza di un determinato database |
> | Azione | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Restituisce un suggerimento sulla scalabilità verticale o orizzontale del database in base alle statistiche di esecuzione query per migliorare le prestazioni o ridurre i costi |
> | Azione | Microsoft.Sql/servers/databases/skus/read | Recupera una raccolta degli SKU disponibili per un database |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Annulla la sincronizzazione del gruppo di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/delete | Elimina un gruppo di sincronizzazione esistente. |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Restituisce l'elenco degli schemi di database dell'hub di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/logs/read | Restituisce l'elenco dei log del gruppo di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/read | Restituisce l'elenco dei gruppi di sincronizzazione o ottiene le proprietà per il gruppo di sincronizzazione specificato. |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Aggiorna lo schema di database dell'hub di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Recupera i risultati dell'operazione di aggiornamento dello schema dell'hub di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Elimina un membro di sincronizzazione esistente. |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Restituisce l'elenco dei membri di sincronizzazione o ottiene le proprietà per il membro di sincronizzazione specificato. |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Aggiorna lo schema del membro di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Recupera i risultati dell'operazione di aggiornamento dello schema del membro di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Restituisce l'elenco degli schemi di database del membro di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Crea un membro di sincronizzazione con i parametri specificati o aggiorna le proprietà per il membro di sincronizzazione specificato. |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Attiva la sincronizzazione del gruppo di sincronizzazione |
> | Azione | Microsoft.Sql/servers/databases/syncGroups/write | Crea un gruppo di sincronizzazione con i parametri specificati o aggiorna le proprietà per il gruppo di sincronizzazione specificato. |
> | Azione | Microsoft.Sql/servers/databases/topQueries/queryText/action | Restituisce il testo Transact-SQL per l'ID query selezionato |
> | Azione | Microsoft.Sql/servers/databases/topQueries/read | Restituisce le statistiche aggregate a runtime per la query selezionata nel periodo di tempo selezionato |
> | Azione | Microsoft.Sql/servers/databases/topQueries/statistics/read | Restituisce le statistiche aggregate a runtime per la query selezionata nel periodo di tempo selezionato |
> | Azione | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Ottiene le operazioni in corso sulla crittografia trasparente dei dati |
> | Azione | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Recupera lo stato e i dettagli della funzionalità di sicurezza di Transparent Data Encryption per uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Cambia lo stato della crittografia trasparente dei dati |
> | Azione | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Aggiorna il database Azure SQL Datawarehouse |
> | Azione | Microsoft.Sql/servers/databases/usages/read | Ottiene le informazioni di utilizzo del database SQL di Azure |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Rimuove la valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Recupera i criteri di valutazione della vulnerabilità in un database specifico |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Rimuove la baseline della regola di valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Ottiene la baseline della regola di valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Modifica la baseline della regola di valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converte un risultato di analisi esistente in un formato leggibile. Se esiste già non accade nulla |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Esegue la scansione del database per la valutazione della vulnerabilità. |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Restituisce l'elenco dei record dell'analisi di valutazione delle vulnerabilità del database o ottiene il record di analisi per l'ID analisi specificato. |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Modifica la valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Esegue la scansione del database per la valutazione della vulnerabilità. |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Recupera il risultato dell'operazione Execute di analisi per la valutazione della vulnerabilità del database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Recupera i dettagli della valutazione di vulnerabilità configurata in uno specifico database |
> | Azione | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Modifica la valutazione delle vulnerabilità per un determinato database |
> | Azione | Microsoft. SQL/Servers/databases/workloadGroups/Delete | Elimina un gruppo di carico di lavoro specifico. |
> | Azione | Microsoft. SQL/Servers/databases/workloadGroups/Read | Elenca i gruppi del carico di lavoro per un database selezionato. |
> | Azione | Microsoft. SQL/Servers/databases/workloadGroups/workloadClassifiers/Delete | Elimina un classificatore del carico di lavoro specifico. |
> | Azione | Microsoft. SQL/Servers/databases/workloadGroups/workloadClassifiers/Read | Elenca i classificatori del carico di lavoro per un database selezionato. |
> | Azione | Microsoft. SQL/Servers/databases/workloadGroups/workloadClassifiers/Write | Imposta le proprietà per un classificatore del carico di lavoro specifico. |
> | Azione | Microsoft. SQL/Servers/databases/workloadGroups/Write | Imposta le proprietà per un gruppo di carico di lavoro specifico. |
> | Azione | Microsoft.Sql/servers/databases/write | Crea un database con i parametri specificati o aggiorna le proprietà o i tag per il database specificato. |
> | Azione | Microsoft.Sql/servers/delete | Elimina un server esistente. |
> | Azione | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Elimina una configurazione di ripristino di emergenza esistente per un determinato server |
> | Azione | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Esegue il failover di una configurazione di ripristino di emergenza |
> | Azione | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forza il failover di una configurazione di ripristino di emergenza |
> | Azione | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Ottiene una raccolta configurazioni di ripristino di emergenza che includono questo server |
> | Azione | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Modifica la configurazione del ripristino di emergenza del server |
> | Azione | Microsoft.Sql/servers/elasticPoolEstimates/read | Restituisce l’elenco di stime del pool elastico già create per questo server |
> | Azione | Microsoft.Sql/servers/elasticPoolEstimates/write | Crea una nuova stima del pool elastico per l'elenco dei database fornito |
> | Azione | Microsoft.Sql/servers/elasticPools/advisors/read | Restituisce l'elenco degli advisor disponibili per il pool elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Restituisce l'elenco di azioni consigliate per l’advisor specificato per il pool elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Applica l'azione consigliata al pool elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/advisors/write | Aggiorna lo stato di esecuzione automatica di un advisor a livello di pool elastico. |
> | Azione | Microsoft.Sql/servers/elasticPools/databases/read | Ottiene un elenco di database per un pool di database elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/delete | Elimina un pool di database elastico esistente |
> | Azione | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Recupera attività e dettagli su un pool di database elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Recupera attività e dettagli su uno specifico database che fa parte del pool di database elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/failover/action | Failover del pool elastico avviato dal cliente. |
> | Azione | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i pool di database elastici |
> | Azione | Microsoft.Sql/servers/elasticPools/metrics/read | Restituisce la metrica per i pool di database elastici |
> | Azione | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Annulla l'operazione asincrona in sospeso del pool elastico di database SQL di Azure che non è ancora stata completata. |
> | Azione | Microsoft.Sql/servers/elasticPools/operations/read | Restituisce l'elenco delle operazioni eseguite nel pool di database elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Ottiene l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Crea o aggiorna l'impostazione di diagnostica per la risorsa |
> | Azione | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metriche disponibili per i pool di database elastici |
> | Azione | Microsoft.Sql/servers/elasticPools/read | Recupera i dettagli del pool di database elastico per un determinato server |
> | Azione | Microsoft.Sql/servers/elasticPools/skus/read | Recupera una raccolta degli SKU disponibili per un pool elastico |
> | Azione | Microsoft.Sql/servers/elasticPools/write | Crea nuove proprietà o modifica quelle esistenti di un pool di database elastico |
> | Azione | Microsoft.Sql/servers/encryptionProtector/read | Ottiene un elenco delle protezioni di crittografia dei server o ottiene le proprietà per la protezione di crittografia del server specificato. |
> | Azione | Microsoft. SQL/Servers/encryptionProtector/revalidate/Action | Aggiorna le proprietà per la protezione di crittografia del server specificato. |
> | Azione | Microsoft.Sql/servers/encryptionProtector/write | Aggiorna le proprietà per la protezione di crittografia del server specificato. |
> | Azione | Microsoft.Sql/servers/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB del server esteso configurati in uno specifico server |
> | Azione | Microsoft.Sql/servers/extendedAuditingSettings/write | Modifica il controllo BLOB del server esteso per uno specifico server |
> | Azione | Microsoft.Sql/servers/failoverGroups/delete | Elimina un gruppo di failover esistente. |
> | Azione | Microsoft.Sql/servers/failoverGroups/failover/action | Esegue il failover pianificato in un gruppo di failover esistente. |
> | Azione | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Esegue il failover forzato in un gruppo di failover esistente. |
> | Azione | Microsoft.Sql/servers/failoverGroups/read | Restituisce l'elenco dei gruppi di failover o ottiene le proprietà per il gruppo di failover specificato. |
> | Azione | Microsoft.Sql/servers/failoverGroups/write | Crea un gruppo di failover con i parametri specificati o aggiorna le proprietà o i tag per il gruppo di failover specificato. |
> | Azione | Microsoft.Sql/servers/firewallRules/delete | Elimina una regola firewall del server esistente. |
> | Azione | Microsoft.Sql/servers/firewallRules/read | Restituisce l'elenco delle regole firewall del server o ottiene le proprietà per la regola del firewall del server specificata. |
> | Azione | Microsoft.Sql/servers/firewallRules/write | Crea una regola firewall del server con i parametri specificati, aggiorna le proprietà per la regola specificata o sovrascrive tutte le regole esistenti con nuove regole firewall del server. |
> | Azione | Microsoft.Sql/servers/import/action | Crea un nuovo database sul server e distribuisce schema e dati da un pacchetto DacPac |
> | Azione | Microsoft.Sql/servers/importExportOperationResults/read | Ottiene le operazioni di importazione/esportazione in corso |
> | Azione | Microsoft. SQL/Servers/inaccessibleDatabases/Read | Restituisce un elenco di database inaccessibili in un server logico. |
> | Azione | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Elimina il profilo dell'endpoint di interfaccia specificato |
> | Azione | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Restituisce le proprietà del profilo dell'endpoint di interfaccia specificato |
> | Azione | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Crea un profilo dell'endpoint di interfaccia con i parametri specificati oppure aggiorna le proprietà o i tag per l'endpoint di interfaccia specificato |
> | Azione | Microsoft.Sql/servers/jobAgents/delete | Elimina un agente processo di database SQL di Azure |
> | Azione | Microsoft.Sql/servers/jobAgents/read | Ottiene un agente processo di database SQL di Azure |
> | Azione | Microsoft.Sql/servers/jobAgents/write | Crea o aggiorna un agente processo di database SQL di Azure |
> | Azione | Microsoft.Sql/servers/keys/delete | Elimina una chiave server esistente. |
> | Azione | Microsoft.Sql/servers/keys/read | Restituisce l'elenco delle chiavi server o ottiene le proprietà per la chiave server specificata. |
> | Azione | Microsoft.Sql/servers/keys/write | Crea una chiave con i parametri specificati o aggiorna le proprietà o i tag per la chiave server specificata. |
> | Azione | Microsoft.Sql/servers/operationResults/read | Ottiene le operazioni del server in corso |
> | Azione | Microsoft. SQL/Servers/Operations/Read | Restituisce l'elenco delle operazioni eseguite nel server |
> | Azione | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Elimina un proxy di connessione all'endpoint privato esistente |
> | Azione | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Restituisce l'elenco dei proxy di connessione all'endpoint privato o ottiene le proprietà per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Convalida una chiamata di creazione di una connessione all'endpoint privato dal lato NRP |
> | Azione | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Crea un proxy di connessione all'endpoint privato con i parametri specificati o aggiorna le proprietà o i tag per il proxy di connessione all'endpoint privato specificato. |
> | Azione | Microsoft.Sql/servers/privateEndpointConnections/delete | Elimina una connessione all'endpoint privato esistente |
> | Azione | Microsoft.Sql/servers/privateEndpointConnections/read | Restituisce l'elenco delle connessioni all'endpoint privato o ottiene le proprietà per la connessione all'endpoint privato specificata. |
> | Azione | Microsoft.Sql/servers/privateEndpointConnections/write | Approva o rifiuta una connessione all'endpoint privato esistente |
> | Azione | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/Action | Determina se l'utente può approvare una connessione all'endpoint privato |
> | Azione | Microsoft.Sql/servers/privateLinkResources/read | Ottenere le risorse di collegamento privato per il server SQL corrispondente |
> | Azione | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Restituisce i tipi di metrica disponibili per i server |
> | Azione | Microsoft.Sql/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Azione | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Recupera le metriche per i pool di database elastici consigliati per un determinato server |
> | Azione | Microsoft.Sql/servers/recommendedElasticPools/read | Recupera il consiglio per i pool di database elastici per ridurre i costi o migliorare le prestazioni in base all'utilizzo storico delle risorse |
> | Azione | Microsoft.Sql/servers/recoverableDatabases/read | Questa operazione viene utilizzata per il ripristino di emergenza di database attivi, per ripristinare il database a un punto di backup noto come valido. Restituisce informazioni sull'ultimo backup valido, ma in realtà non ripristina il database. |
> | Azione | Microsoft.Sql/servers/replicationLinks/read | Restituisce l'elenco dei collegamenti di replica o ottiene le proprietà dei collegamenti di replica specificati. |
> | Azione | Microsoft.Sql/servers/restorableDroppedDatabases/read | Ottiene un elenco di database eliminati su uno specifico server che rientrano ancora nei criteri di conservazione. |
> | Azione | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Recupera i risultati dell'operazione di scrittura dei criteri di rilevamento minacce del server |
> | Azione | Microsoft.Sql/servers/securityAlertPolicies/read | Recupera un elenco di criteri di rilevamento minacce del server configurati per un determinato server |
> | Azione | Microsoft.Sql/servers/securityAlertPolicies/write | Modifica i criteri di rilevamento minacce del server per uno specifico server |
> | Azione | Microsoft.Sql/servers/serviceObjectives/read | Recupera l'elenco degli obiettivi del livello di servizio (anche noti come livelli di prestazioni) disponibili in un determinato server |
> | Azione | Microsoft.Sql/servers/syncAgents/delete | Elimina un agente di sincronizzazione esistente. |
> | Azione | Microsoft.Sql/servers/syncAgents/generateKey/action | Genera la chiave di registrazione dell'agente di sincronizzazione |
> | Azione | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Restituisce l'elenco dei database collegati dell'agente di sincronizzazione |
> | Azione | Microsoft.Sql/servers/syncAgents/read | Restituisce l'elenco degli agenti di sincronizzazione o ottiene le proprietà per l'agente di sincronizzazione specificato. |
> | Azione | Microsoft.Sql/servers/syncAgents/write | Crea un agente di sincronizzazione con i parametri specificati o aggiorna le proprietà per l'agente di sincronizzazione specificato. |
> | Azione | Microsoft.Sql/servers/tdeCertificates/action | Crea/aggiorna un certificato TDE |
> | Azione | Microsoft.Sql/servers/usages/read | Restituisce la quota DTU del server e il consumo corrente di DTU da parte di tutti i database all'interno del server |
> | Azione | Microsoft.Sql/servers/virtualNetworkRules/delete | Elimina una regola di rete virtuale esistente |
> | Azione | Microsoft.Sql/servers/virtualNetworkRules/read | Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata. |
> | Azione | Microsoft.Sql/servers/virtualNetworkRules/write | Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata. |
> | Azione | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Rimuove la valutazione delle vulnerabilità per un determinato server |
> | Azione | Microsoft.Sql/servers/vulnerabilityAssessments/read | Recupera i criteri di valutazione della vulnerabilità in un server specifico |
> | Azione | Microsoft.Sql/servers/vulnerabilityAssessments/write | Modifica la valutazione delle vulnerabilità per un determinato server |
> | Azione | Microsoft.Sql/servers/write | Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato. |
> | Azione | Microsoft.Sql/unregister/action | Annulla la registrazione della sottoscrizione per il provider di risorse del database Microsoft SQL e consente la creazione di database Microsoft SQL. |
> | Azione | Microsoft.Sql/virtualClusters/delete | Elimina un cluster virtuale esistente. |
> | Azione | Microsoft.Sql/virtualClusters/read | Restituisce l'elenco di cluster virtuali o ottiene le proprietà per il cluster virtuale specificato. |
> | Azione | Microsoft.Sql/virtualClusters/write | Aggiorna i tag di cluster virtuale. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. SqlVirtualMachine/locations/availabilityGroupListenerOperationResults/Read | Ottenere il risultato di un'operazione del listener del gruppo di disponibilità |
> | Azione | Microsoft. SqlVirtualMachine/locations/sqlVirtualMachineGroupOperationResults/Read | Ottenere il risultato di un'operazione del gruppo di macchine virtuali SQL |
> | Azione | Microsoft. SqlVirtualMachine/locations/sqlVirtualMachineOperationResults/Read | Ottenere il risultato dell'operazione della macchina virtuale SQL |
> | Azione | Microsoft. SqlVirtualMachine/Operations/Read |  |
> | Azione | Microsoft. SqlVirtualMachine/Register/Action | Registrare la sottoscrizione con il provider di risorse Microsoft. SqlVirtualMachine |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Delete | Elimina listener gruppo di disponibilità esistente |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Read | Recupera i dettagli del listener del gruppo di disponibilità SQL in un gruppo di macchine virtuali SQL specificato |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Write | Crea una nuova proprietà o modifica le proprietà del listener del gruppo di disponibilità SQL esistente |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Delete | Elimina gruppo di macchine virtuali SQL esistente |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Read | Recuperare dettagli del gruppo di macchine virtuali SQL |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/Read | Elencare le macchine virtuali SQL per un determinato gruppo di macchine virtuali SQL |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Write | Crea una nuova proprietà o modifica le proprietà del gruppo di macchine virtuali SQL esistente |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Delete | Elimina macchina virtuale SQL esistente |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Read | Recupera i dettagli della macchina virtuale SQL |
> | Azione | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Write | Crea una nuova proprietà o modifica le proprietà della macchina virtuale SQL esistente |
> | Azione | Microsoft. SqlVirtualMachine/Annulla registrazione/azione | Annulla la registrazione della sottoscrizione con il provider di risorse Microsoft. SqlVirtualMachine |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Storage/checknameavailability/read | Controlla che il nome dell’account sia valido e non in uso. |
> | Azione | Microsoft.Storage/locations/checknameavailability/read | Controlla che il nome dell’account sia valido e non in uso. |
> | Azione | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Avvisa Microsoft.Storage che la rete virtuale o la subnet è in fase di eliminazione |
> | Azione | Microsoft.Storage/locations/usages/read | Restituisce il limite e il conteggio corrente degli utilizzi per le risorse nella sottoscrizione specificata |
> | Azione | Microsoft.Storage/operations/read | Interroga sullo stato di un’operazione asincrona. |
> | Azione | Microsoft.Storage/register/action | Registra la sottoscrizione per il provider di risorse di archiviazione e consente la creazione di account di archiviazione. |
> | Azione | Microsoft.Storage/skus/read | Elenca gli SKU supportati da Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Restituisce il risultato dell'aggiunta di contenuto BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | DataAction | Microsoft. storage/storageAccounts/blobServices/Containers/Blobs/deleteBlobVersion/Action | Restituisce il risultato dell'eliminazione di una versione BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Restituisce l'elenco di BLOB in un account con filtro tag corrispondente |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Restituisce il risultato del comando BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Restituisce il risultato della lettura dei tag BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Restituisce il risultato della scrittura dei tag BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Restituisce il risultato della scrittura su un BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Cancella il blocco a fini giudiziari del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Restituisce il risultato dell'eliminazione di un contenitore |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Elimina i criteri di immutabilità del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Estendi i criteri di immutabilità del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blocca i criteri di immutabilità del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Recupera i criteri di immutabilità del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Crea/Aggiorna i criteri di immutabilità del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Restituisce il risultato del leasing del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce un contenitore |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce l'elenco dei contenitori |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Imposta il blocco a fini giudiziari del contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/write | Restituisce il risultato dell'applicazione della patch al contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/containers/write | Restituisce il risultato dell'operazione PUT sul contenitore BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/read | Restituisce statistiche o proprietà del servizio BLOB |
> | Azione | Microsoft.Storage/storageAccounts/blobServices/write | Restituisce il risultato della creazione/aggiornamento delle proprietà del servizio BLOB |
> | Azione | Microsoft.Storage/storageAccounts/delete | Elimina un account di archiviazione esistente. |
> | Azione | Microsoft. storage/storageAccounts/encryptionScopes/Read |  |
> | Azione | Microsoft. storage/storageAccounts/encryptionScopes/Read |  |
> | Azione | Microsoft. storage/storageAccounts/encryptionScopes/Write |  |
> | Azione | Microsoft. storage/storageAccounts/encryptionScopes/Write |  |
> | Azione | Microsoft.Storage/storageAccounts/failover/action | Il cliente è in grado di controllare il failover in caso di problemi di disponibilità |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Ottenere i privilegi di amministratore di file |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Restituisce il risultato dell'eliminazione di un file o di una cartella |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Restituisce il risultato della modifica dell'autorizzazione per un file o una cartella |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Restituisce un file o una cartella o un elenco di file/cartelle |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Restituisce il risultato della scrittura di un file o della creazione di una cartella |
> | Azione | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Azione | Microsoft.Storage/storageAccounts/fileServices/read | Ottenere le proprietà del servizio file |
> | Azione | Microsoft. storage/storageAccounts/fileservices/shares/Delete |  |
> | Azione | Microsoft. storage/storageAccounts/fileservices/shares/Read |  |
> | Azione | Microsoft. storage/storageAccounts/fileservices/shares/Read |  |
> | Azione | Microsoft. storage/storageAccounts/fileservices/shares/Write |  |
> | Azione | Microsoft. storage/storageAccounts/FileService/scrittura |  |
> | Azione | Microsoft.Storage/storageAccounts/listAccountSas/action | Restituisce il token SAS dell’account per l’account di archiviazione specificato. |
> | Azione | Microsoft.Storage/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Azione | Microsoft.Storage/storageAccounts/listServiceSas/action | Restituisce il token di firma di accesso condiviso del servizio per l'account di archiviazione specificato. |
> | Azione | Microsoft.Storage/storageAccounts/managementPolicies/delete | Eliminare i criteri di gestione degli account di archiviazione |
> | Azione | Microsoft.Storage/storageAccounts/managementPolicies/read | Ottenere i criteri dell'account di gestione archiviazione |
> | Azione | Microsoft.Storage/storageAccounts/managementPolicies/write | Inserire i criteri di gestione degli account di archiviazione |
> | Azione | Microsoft. storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Azione | Microsoft. storage/storageAccounts/objectReplicationPolicies/Read |  |
> | Azione | Microsoft. storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Azione | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Elimina proxy di connessione all'endpoint privato |
> | Azione | Microsoft. storage/storageAccounts/privateEndpointConnectionProxies/Read | Ottenere il proxy di connessione all'endpoint privato |
> | Azione | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Inserire proxy di connessione all'endpoint privato |
> | Azione | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Elimina connessione endpoint privato |
> | Azione | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Ottenere la connessione all'endpoint privato |
> | Azione | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Inserire la connessione all'endpoint privato |
> | Azione | Microsoft. storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Approva connessioni a endpoint privato |
> | Azione | Microsoft.Storage/storageAccounts/privateLinkResources/read | Ottenere StorageAccount GroupID |
> | Azione | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Restituisce il risultato dell'eliminazione di una coda |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Restituisce il risultato dell'aggiunta di un messaggio |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Restituisce il risultato dell'eliminazione di un messaggio |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Restituisce il risultato dell'elaborazione di un messaggio |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Restituisce un messaggio |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Restituisce il risultato della scrittura di un messaggio |
> | Azione | Microsoft.Storage/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | Azione | Microsoft.Storage/storageAccounts/queueServices/queues/write | Restituisce il risultato della scrittura di una coda |
> | Azione | Microsoft.Storage/storageAccounts/queueServices/read | Ottenere Servizio di accodamento proprietà |
> | Azione | Microsoft.Storage/storageAccounts/queueServices/read | Restituisce statistiche o proprietà del servizio di accodamento. |
> | Azione | Microsoft.Storage/storageAccounts/queueServices/write | Restituisce il risultato dell'impostazione delle proprietà del servizio di accodamento |
> | Azione | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Azione | Microsoft.Storage/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso per l'account di archiviazione specificato. |
> | Azione | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Ripristina gli intervalli di BLOB allo stato dell'ora specificata |
> | Azione | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revoca tutte le chiavi di delega utente dell'account di archiviazione specificato. |
> | Azione | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Crea/Aggiorna le impostazioni di diagnostica dell’account di archiviazione. |
> | Azione | Microsoft.Storage/storageAccounts/tableServices/read | Ottenere le proprietà del servizio tabelle |
> | Azione | Microsoft.Storage/storageAccounts/write | Crea un account di archiviazione con i parametri specificati o aggiorna le proprietà o i tag o aggiunge un dominio personalizzato per l’account di archiviazione specificato. |
> | Azione | Microsoft.Storage/usages/read | Restituisce il limite e il conteggio corrente degli utilizzi per le risorse nella sottoscrizione specificata |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | microsoft.storagesync/locations/checkNameAvailability/action | Controlla che il nome del servizio di sincronizzazione archiviazione sia valido e non in uso |
> | Azione | microsoft.storagesync/locations/workflows/operations/read | Ottiene lo stato di un'operazione asincrona |
> | Azione | Microsoft. StorageSync/Operations/Read | Ottiene un elenco delle operazioni supportate |
> | Azione | Microsoft. StorageSync/Register/Action | Registra la sottoscrizione per il provider di sincronizzazione archiviazione |
> | Azione | microsoft.storagesync/storageSyncServices/delete | Elimina tutti i servizi di sincronizzazione archiviazione |
> | Azione | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per i servizi di sincronizzazione archiviazione |
> | Azione | microsoft.storagesync/storageSyncServices/read | Legge tutti i servizi di sincronizzazione archiviazione |
> | Azione | microsoft.storagesync/storageSyncServices/registeredServers/delete | Elimina tutti i server registrati |
> | Azione | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per il server registrato |
> | Azione | microsoft.storagesync/storageSyncServices/registeredServers/read | Legge tutti i server registrati |
> | Azione | microsoft.storagesync/storageSyncServices/registeredServers/write | Crea o aggiorna tutti i server registrati |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Elimina tutti gli endpoint cloud |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Ottiene lo stato di un'operazione asincrona di backup/ripristino |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chiama questa operazione al termine del backup |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chiama questa operazione al termine del ripristino |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chiama questa operazione prima del backup |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chiama questa operazione prima del ripristino |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Legge tutti gli endpoint cloud |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Ripristina l'heartbeat |
> | Azione | Microsoft. StorageSync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/Action | Chiamare questa azione per attivare il rilevamento delle modifiche nella condivisione file di un endpoint cloud |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Crea o aggiorna tutti gli endpoint cluster |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/delete | Elimina tutti i gruppi di sincronizzazione |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Ottiene le metriche disponibili per i gruppi di sincronizzazione |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/read | Legge tutti i gruppi di sincronizzazione |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Elimina tutti gli endpoint server |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Ottiene le metriche disponibili per gli endpoint server |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Legge tutti gli endpoint server |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chiama questa azione per richiamare i file in un server |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Crea o aggiorna tutti gli endpoint server |
> | Azione | microsoft.storagesync/storageSyncServices/syncGroups/write | Crea o aggiorna tutti i gruppi di sincronizzazione |
> | Azione | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Ottiene lo stato di un'operazione asincrona |
> | Azione | microsoft.storagesync/storageSyncServices/workflows/operations/read | Ottiene lo stato di un'operazione asincrona |
> | Azione | microsoft.storagesync/storageSyncServices/workflows/read | Legge i flussi di lavoro |
> | Azione | microsoft.storagesync/storageSyncServices/write | Crea o aggiorna tutti i servizi di sincronizzare archiviazione |
> | Azione | Microsoft. StorageSync/Annulla registrazione/azione | Annulla la registrazione della sottoscrizione per il provider di sincronizzazione archiviazione |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.StorSimple/managers/accessControlRecords/delete | Elimina i record di controllo di accesso |
> | Azione | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/accessControlRecords/read | Elenca o ottiene i record di controllo di accesso |
> | Azione | Microsoft.StorSimple/managers/accessControlRecords/write | Crea o aggiorna i record di controllo di accesso |
> | Azione | Microsoft.StorSimple/managers/alerts/read | Elenca o ottiene gli avvisi |
> | Azione | Microsoft.StorSimple/managers/backups/read | Elenca o ottiene i set di backup |
> | Azione | Microsoft.StorSimple/managers/bandwidthSettings/delete | Elimina impostazioni di larghezza di banda esistenti (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/bandwidthSettings/read | Elenca le impostazioni di larghezza di banda (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/bandwidthSettings/write | Crea una nuova impostazione o aggiorna le impostazioni di larghezza di banda esistenti (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/certificates/write | Crea o aggiorna i certificati |
> | Azione | Microsoft.StorSimple/Managers/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali per la risorsa e/o l'insieme di credenziali. |
> | Azione | Microsoft.StorSimple/managers/clearAlerts/action | Cancella tutti gli avvisi associati a Gestione dispositivi. |
> | Azione | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Elenca le configurazioni supportate dall'appliance cloud |
> | Azione | Microsoft.StorSimple/managers/configureDevice/action | Configura un dispositivo |
> | Azione | Microsoft.StorSimple/managers/delete | Elimina gli strumenti di gestione dei dispositivi |
> | Azione | Microsoft.StorSimple/Managers/delete | L'operazione Elimina insieme di credenziali elimina la risorsa di Azure specificata di tipo 'vault' |
> | Azione | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/alertSettings/read | Elenca o ottiene le impostazioni degli avvisi |
> | Azione | Microsoft.StorSimple/managers/devices/alertSettings/write | Crea o aggiorna le impostazioni degli avvisi |
> | Azione | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizza il rollover della chiave di crittografia del servizio dei dispositivi |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | A partire da un backup manuale crea un backup su richiesta di tutti i volumi protetti dai criteri. |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Elimina criteri di backup esistenti (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/read | Elenca i criteri di backup (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Elimina le pianificazioni esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Elenca le pianificazioni |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Crea pianificazioni o aggiorna pianificazioni esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/backupPolicies/write | Crea nuovi criteri di backup o aggiorna quelli esistenti (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/devices/backups/delete | Elimina il set di backup |
> | Azione | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clona una condivisione o volume usando un elemento di backup. |
> | Azione | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/backups/read | Elenca o ottiene i set di backup |
> | Azione | Microsoft.StorSimple/managers/devices/backups/restore/action | Ripristina tutti i volumi da un set di backup. |
> | Azione | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Elimina i gruppi di pianificazione del backup |
> | Azione | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Elenca o ottiene i gruppi di pianificazione del backup |
> | Azione | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Crea o aggiorna i gruppi di pianificazione del backup |
> | Azione | Microsoft.StorSimple/managers/devices/chapSettings/delete | Elimina le impostazioni CHAP |
> | Azione | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/chapSettings/read | Elenca o ottiene le impostazioni CHAP |
> | Azione | Microsoft.StorSimple/managers/devices/chapSettings/write | Crea o aggiorna le impostazioni CHAP |
> | Azione | Microsoft.StorSimple/managers/devices/deactivate/action | Disattiva un dispositivo. |
> | Azione | Microsoft.StorSimple/managers/devices/delete | Elimina i dispositivi |
> | Azione | Microsoft.StorSimple/managers/devices/disks/read | Elenca o ottiene i dischi |
> | Azione | Microsoft.StorSimple/managers/devices/download/action | Scaricare gli aggiornamenti per un dispositivo. |
> | Azione | Microsoft.StorSimple/managers/devices/failover/action | Failover del dispositivo. |
> | Azione | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/failoverTargets/read | Elenca o ottiene le destinazioni di failover dei dispositivi |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Esegue il backup di un file server. |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/delete | Elimina i file server |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Elenca o ottiene le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Elenca o ottiene le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/read | Elenca o ottiene i file server |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Elimina le condivisioni |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Elenca o ottiene le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Elenca o ottiene le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Elenca o ottiene le condivisioni |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Crea o aggiorna le condivisioni |
> | Azione | Microsoft.StorSimple/managers/devices/fileservers/write | Crea o aggiorna i file server |
> | Azione | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Modifica lo stato di alimentazione del controller dei gruppi di componenti hardware |
> | Azione | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Elenca i gruppi di componenti hardware |
> | Azione | Microsoft.StorSimple/managers/devices/install/action | Installa aggiornamenti in un dispositivo. |
> | Azione | Microsoft.StorSimple/managers/devices/installUpdates/action | Installa gli aggiornamenti nei dispositivi (solo serie 8000). |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Esegue il backup di un server iSCSI. |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Elimina i server iSCSI |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Elimina i dischi |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Elenca o ottiene le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Elenca o ottiene le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Elenca o ottiene i dischi |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Crea o aggiorna i dischi |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Elenca o ottiene le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Elenca o ottiene le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/read | Elenca o ottiene i server iSCSI |
> | Azione | Microsoft.StorSimple/managers/devices/iscsiservers/write | Crea o aggiorna i server iSCSI |
> | Azione | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Annulla un processo in esecuzione |
> | Azione | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/jobs/read | Elenca o ottiene i processi |
> | Azione | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Elenca i set di failover per un dispositivo esistente (solo serie 8000). |
> | Azione | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Elenca le destinazioni di failover dei dispositivi (solo serie 8000). |
> | Azione | Microsoft.StorSimple/managers/devices/metrics/read | Elenca o ottiene le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Elenca o ottiene le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Conferma una migrazione completata correttamente e ne esegue il commit. |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Elenca lo stato di conferma della migrazione |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Recupera lo stato di conferma della migrazione. |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Recupera lo stato del processo di stima della migrazione. |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Recupera lo stato della migrazione. |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importa le configurazioni di origine per la migrazione |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Elenca la stima della migrazione |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Elenca lo stato della migrazione |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Avvia la migrazione usando le configurazioni di origine |
> | Azione | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Avvia un processo per stimare la durata del processo di migrazione. |
> | Azione | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/networkSettings/read | Elenca o ottiene le impostazioni di rete |
> | Azione | Microsoft.StorSimple/managers/devices/networkSettings/write | Crea nuove impostazioni di rete o aggiorna quelle esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Elenca le chiavi di crittografia pubbliche di Gestione dispositivi |
> | Azione | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Pubblica il pacchetto di supporto per un dispositivo esistente. Un pacchetto per il supporto StorSimple è un meccanismo semplice da usare che raccoglie tutti i log pertinenti per aiutare il supporto tecnico Microsoft a risolvere i problemi relativi ai dispositivi StorSimple. |
> | Azione | Microsoft.StorSimple/managers/devices/read | Elenca o ottiene i dispositivi |
> | Azione | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Verifica la disponibilità di aggiornamenti per un dispositivo. |
> | Azione | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/securitySettings/read | Elenca le impostazioni di protezione |
> | Azione | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronizza il certificato di gestione remota per un dispositivo. |
> | Azione | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Aggiorna le impostazioni di protezione. |
> | Azione | Microsoft.StorSimple/managers/devices/securitySettings/write | Crea nuove impostazioni di protezione o aggiorna quelle esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Invia messaggi di posta elettronica di avviso di test ai destinatari di posta elettronica configurati. |
> | Azione | Microsoft.StorSimple/managers/devices/shares/read | Elenca o ottiene le condivisioni |
> | Azione | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/timeSettings/read | Elenca o ottiene le impostazioni ora |
> | Azione | Microsoft.StorSimple/managers/devices/timeSettings/write | Crea nuove impostazioni ora o aggiorna quelle esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/updateSummary/read | Elenca o ottiene il riepilogo aggiornamenti |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Elenca un contenitore del volume esistente (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Elenca le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Elenca le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/read | Elenca i contenitori del volume (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Elimina i volumi esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Elenca le metriche |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Elenca le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Elenca i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Elenca i volumi |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Crea nuovi volumi o aggiorna quelli esistenti |
> | Azione | Microsoft.StorSimple/managers/devices/volumeContainers/write | Crea nuovi contenitori del volume o aggiorna quelli esistenti (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/devices/volumes/read | Elenca i volumi |
> | Azione | Microsoft.StorSimple/managers/devices/write | Crea o aggiorna i dispositivi |
> | Azione | Microsoft.StorSimple/managers/encryptionSettings/read | Elenca o ottiene le impostazioni di crittografia |
> | Azione | Microsoft.StorSimple/managers/extendedInformation/delete | Elimina le informazioni estese dell'insieme di credenziali |
> | Azione | Microsoft.StorSimple/Managers/extendedInformation/delete | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Azione | Microsoft.StorSimple/managers/extendedInformation/read | Elenca o ottiene le informazioni estese dell'insieme di credenziali |
> | Azione | Microsoft.StorSimple/Managers/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Azione | Microsoft.StorSimple/managers/extendedInformation/write | Crea o aggiorna le informazioni estese dell'insieme di credenziali |
> | Azione | Microsoft.StorSimple/Managers/extendedInformation/write | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Azione | Microsoft.StorSimple/managers/features/read | Elenca le funzionalità |
> | Azione | Microsoft.StorSimple/managers/fileservers/read | Elenca o ottiene i file server |
> | Azione | Microsoft.StorSimple/managers/getEncryptionKey/action | Ottiene la chiave di crittografia per Gestione dispositivi. |
> | Azione | Microsoft.StorSimple/managers/iscsiservers/read | Elenca o ottiene i server iSCSI |
> | Azione | Microsoft.StorSimple/managers/jobs/read | Elenca o ottiene i processi |
> | Azione | Microsoft.StorSimple/managers/listActivationKey/action | Ottiene la chiave di attivazione di Gestione dispositivi StorSimple. |
> | Azione | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Elenca le chiavi di crittografia pubbliche di uno strumento Gestione dispositivi StorSimple. |
> | Azione | Microsoft.StorSimple/managers/metrics/read | Elenca o ottiene le metriche |
> | Azione | Microsoft.StorSimple/managers/metricsDefinitions/read | Elenca o ottiene le definizioni di metrica |
> | Azione | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Esegue la migrazione dal modello classico a Resource Manager |
> | Azione | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Elencare le configurazioni di origine della migrazione (solo serie 8000) |
> | Azione | Microsoft.StorSimple/managers/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Crea una nuova appliance cloud. |
> | Azione | Microsoft.StorSimple/managers/read | Elenca o ottiene gli strumenti di gestione dei dispositivi |
> | Azione | Microsoft.StorSimple/Managers/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Azione | Microsoft.StorSimple/managers/regenerateActivationKey/action | Rigenera la chiave di attivazione per un'istanza esistente di Gestione dispositivi StorSimple. |
> | Azione | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Elimina le credenziali dell'account di archiviazione |
> | Azione | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/storageAccountCredentials/read | Elenca o ottiene le credenziali dell'account di archiviazione |
> | Azione | Microsoft.StorSimple/managers/storageAccountCredentials/write | Crea o aggiorna le credenziali dell'account di archiviazione |
> | Azione | Microsoft.StorSimple/managers/storageDomains/delete | Elimina i domini di archiviazione |
> | Azione | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Elenca o ottiene i risultati dell'operazione |
> | Azione | Microsoft.StorSimple/managers/storageDomains/read | Elenca o ottiene i domini di archiviazione |
> | Azione | Microsoft.StorSimple/managers/storageDomains/write | Crea o aggiorna i domini di archiviazione |
> | Azione | Microsoft.StorSimple/managers/write | Crea o aggiorna gli strumenti di gestione dei dispositivi |
> | Azione | Microsoft.StorSimple/Managers/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | Azione | Microsoft.StorSimple/operations/read | Elenca o ottiene le operazioni |
> | Azione | Microsoft.StorSimple/register/action | Registra il provider Microsoft StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.StreamAnalytics/locations/quotas/Read | Legge la quota sottoscrizione di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/operations/Read | Legge le operazioni di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/Register/action | Registra la sottoscrizione con il provider di risorse di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/Delete | Elimina un processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Elimina la funzione del processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Legge i risultati dell'operazione per la funzione di processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Legge la funzione del processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Recupera la definizione predefinita di una funzione di processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Esegue il test della funzione del processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Scrive la funzione del processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Elimina un input processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Legge i risultati dell'operazione per l'input di processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Legge un input processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Campiona l'input di processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Esegue il test dell'input processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Scrive un input processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Legge le definizioni della metrica |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Legge i risultati dell'operazione per il processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Elimina un output processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Legge i risultati dell'operazione per l'output di processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Legge un output processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Esegue il test dell'output processo di Analisi di flusso di Azure |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Scrive un output processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Legge un'impostazione di diagnostica. |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Scrive un'impostazione di diagnostica. |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Ottiene i log disponibili per i processi di streaming |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Ottiene la metrica disponibile per i processi di streaming |
> | Azione | Microsoft. StreamAnalytics/streaming/PublishEdgePackage/Action | Pubblicare un pacchetto Edge per un processo di analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/Read | Legge un processo di Analisi di flusso |
> | Azione | Microsoft. StreamAnalytics/streaming/scale/Action | Ridimensionare un processo di analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/Start/action | Avvia un processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Arresta un processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Elimina una trasformazione processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Crea una trasformazione processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Scrive una trasformazione processo di Analisi di flusso |
> | Azione | Microsoft.StreamAnalytics/streamingjobs/Write | Scrive un processo di Analisi di flusso |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft. Subscription/Annulla/azione | Annulla la sottoscrizione |
> | Azione | Microsoft.Subscription/CreateSubscription/action | Creare una sottoscrizione di Azure |
> | Azione | Microsoft.Subscription/register/action | Registra la sottoscrizione per il provider di risorse Microsoft.Subscription |
> | Azione | Microsoft. Subscription/Rinomina/azione | Rinomina la sottoscrizione |
> | Azione | Microsoft.Subscription/SubscriptionDefinitions/read | Ottiene una definizione di sottoscrizione di Azure all'interno di un gruppo di gestione. |
> | Azione | Microsoft.Subscription/SubscriptionDefinitions/write | Crea una definizione di sottoscrizione di Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.Support/register/action | Esegue la registrazione al provider di risorse di supporto |
> | Azione | Microsoft.Support/supportTickets/read | Ottiene i dettagli del ticket di supporto (inclusi stato, gravità, dettagli di contatto e comunicazioni) oppure ottiene l'elenco dei ticket di supporto dalle diverse sottoscrizioni. |
> | Azione | Microsoft.Support/supportTickets/write | Crea o aggiorna un ticket di supporto. È possibile creare un ticket di supporto per problemi associati ai settori Tecnico, Fatturazione, Quote o Gestione della sottoscrizione. È possibile aggiornare la gravità, i dettagli di contatto e le comunicazioni dei ticket di supporto esistenti. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Elimina i criteri di accesso. |
> | Azione | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Ottiene le proprietà dei criteri di accesso. |
> | Azione | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Crea nuovi criteri di accesso per un ambiente o aggiorna criteri di accesso esistenti. |
> | Azione | Microsoft.TimeSeriesInsights/environments/delete | Elimina l'ambiente. |
> | Azione | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Elimina l'origine evento. |
> | Azione | Microsoft.TimeSeriesInsights/environments/eventsources/read | Ottiene le proprietà di una origine evento. |
> | Azione | Microsoft.TimeSeriesInsights/environments/eventsources/write | Crea una nuova origine evento per un ambiente o aggiorna un'origine evento esistente. |
> | Azione | Microsoft.TimeSeriesInsights/environments/read | Ottiene le proprietà di un ambiente. |
> | Azione | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Elimina il set di dati di riferimento. |
> | Azione | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Ottiene le proprietà di un set di dati di riferimento. |
> | Azione | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Crea un nuovo set di dati di riferimento per un ambiente o aggiorna un set di dati di riferimento esistente. |
> | Azione | Microsoft.TimeSeriesInsights/environments/status/read | Ottiene lo stato dell'ambiente, lo stato delle operazioni associate, ad esempio i dati in ingresso. |
> | Azione | Microsoft.TimeSeriesInsights/environments/write | Crea un nuovo ambiente o aggiorna un ambiente esistente. |
> | Azione | Microsoft.TimeSeriesInsights/register/action | Registra la sottoscrizione per il provider di risorse Time Series Insights e consente la creazione di ambienti Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.VisualStudio/Account/Delete | Elimina l'account |
> | Azione | Microsoft.VisualStudio/Account/Extension/Read | Legge l'account o l'estensione |
> | Azione | Microsoft.VisualStudio/Account/Project/Read | Legge l'account o il progetto |
> | Azione | Microsoft.VisualStudio/Account/Project/Write | Imposta l'account o il progetto |
> | Azione | Microsoft.VisualStudio/Account/Read | Legge l'account |
> | Azione | Microsoft.VisualStudio/Account/Write | Imposta l'account |
> | Azione | Microsoft.VisualStudio/Extension/Delete | Elimina l'estensione |
> | Azione | Microsoft.VisualStudio/Extension/Read | Legge l'estensione |
> | Azione | Microsoft.VisualStudio/Extension/Write | Imposta l'estensione |
> | Azione | Microsoft.VisualStudio/Project/Delete | Elimina il progetto |
> | Azione | Microsoft.VisualStudio/Project/Read | Legge il progetto |
> | Azione | Microsoft.VisualStudio/Project/Write | Imposta il progetto |
> | Azione | Microsoft.VisualStudio/Register/Action | Registra la sottoscrizione di Azure con il provider Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | microsoft.web/apimanagementaccounts/apiacls/read | Ottiene gli ACL API dell'account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Elimina le ACL API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/apiacls/read | Ottiene gli ACL API dell'account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/apiacls/write | Aggiorna ACL API di account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Ottiene gli ACL di connessione delle API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Conferma le connessioni API di account di Gestione API con codice di consenso. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Elimina le ACL di connessione delle connessioni API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Ottiene gli ACL di connessione delle connessioni API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Aggiorna ACL di connessione delle connessioni delle API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/delete | Elimina le connessioni API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Ottiene collegamenti di consenso per connessioni API di account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Elenca le connessioni API degli account di Gestione API con chiavi di connessione. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Elenca le connessioni API degli account di gestione API con segreti. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/read | Ottiene le connessioni API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/connections/write | Aggiorna le connessioni API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/delete | Elimina le API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Elimina le definizioni localizzate delle API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Ottiene le definizioni localizzate delle API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Aggiorna le definizioni localizzate delle API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/read | Ottiene le API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/apis/write | Aggiorna le API degli account di Gestione API. |
> | Azione | microsoft.web/apimanagementaccounts/connectionacls/read | Ottiene gli ACL di connessione degli account di Gestione API. |
> | Azione | microsoft.web/availablestacks/read | Ottiene gli stack disponibili. |
> | Azione | Microsoft.Web/certificates/Delete | Elimina un certificato esistente. |
> | Azione | Microsoft.Web/certificates/Read | Ottiene l'elenco dei certificati. |
> | Azione | Microsoft.Web/certificates/Write | Aggiunge un nuovo certificato o ne aggiorna uno esistente. |
> | Azione | microsoft.web/checknameavailability/read | Verifica se il nome della risorsa è disponibile. |
> | Azione | microsoft.web/classicmobileservices/read | Ottiene servizi mobili classici. |
> | Azione | Microsoft.Web/connectionGateways/Delete | Elimina un gateway di connessione. |
> | Azione | Microsoft.Web/connectionGateways/Join/Action | Unisce un gateway di connessione. |
> | Azione | Microsoft.Web/connectionGateways/ListStatus/Action | Elenca lo stato di un gateway di connessione. |
> | Azione | Microsoft.Web/connectionGateways/Move/Action | Sposta un gateway di connessione. |
> | Azione | Microsoft.Web/connectionGateways/Read | Ottiene l'elenco dei gateway di connessione. |
> | Azione | Microsoft.Web/connectionGateways/Write | Crea o aggiorna un gateway di connessione. |
> | Azione | microsoft.web/connections/confirmconsentcode/action | Conferma il codice di consenso delle connessioni. |
> | Azione | Microsoft.Web/connections/Delete | Elimina una connessione. |
> | Azione | Microsoft.Web/connections/Join/Action | Unisce una connessione. |
> | Azione | microsoft.web/connections/listconsentlinks/action | Elenca i collegamenti di consenso per le connessioni. |
> | Azione | Microsoft.Web/connections/Move/Action | Sposta una connessione. |
> | Azione | Microsoft.Web/connections/Read | Ottiene l'elenco delle connessioni. |
> | Azione | Microsoft.Web/connections/Write | Crea o aggiorna una connessione. |
> | Azione | Microsoft.Web/customApis/Delete | Elimina un'API personalizzata. |
> | Azione | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Estrae la definizione dell'API da WSDL. |
> | Azione | Microsoft.Web/customApis/Join/Action | Aggiunge un'API personalizzata. |
> | Azione | Microsoft.Web/customApis/listWsdlInterfaces/Action | Elenca le interfacce WSDL per un'API personalizzata. |
> | Azione | Microsoft.Web/customApis/Move/Action | Sposta un'API personalizzata. |
> | Azione | Microsoft.Web/customApis/Read | Ottiene l'elenco delle API personalizzate. |
> | Azione | Microsoft.Web/customApis/Write | Crea o aggiorna un'API personalizzata. |
> | Azione | Microsoft.Web/deletedSites/Read | Ottiene le proprietà di un'app Web eliminata |
> | Azione | microsoft.web/deploymentlocations/read | Ottiene i percorsi di distribuzione. |
> | Azione | Microsoft.Web/geoRegions/Read | Ottiene l'elenco delle aree geografiche. |
> | Azione | microsoft.web/hostingenvironments/capacities/read | Ottiene le capacità degli ambienti di hosting. |
> | Azione | Microsoft.Web/hostingEnvironments/Delete | Elimina un ambiente del servizio app |
> | Azione | microsoft.web/hostingenvironments/detectors/read | Ottiene i rilevamenti degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/diagnostics/read | Ottiene la diagnostica degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Ottiene gli endpoint di rete di tutte le dipendenze in ingresso. |
> | Azione | Microsoft.Web/hostingEnvironments/Join/Action | Aggiunge un ambiente del servizio app |
> | Azione | microsoft.web/hostingenvironments/metricdefinitions/read | Ottiene le definizioni metrica degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Ottiene le definizioni metrica dei pool multiruolo degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/multirolepools/metrics/read | Ottiene la metrica dei pool multiruolo degli ambienti di hosting. |
> | Azione | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Ottiene le proprietà di un pool front-end in un ambiente del servizio app |
> | Azione | microsoft.web/hostingenvironments/multirolepools/skus/read | Ottiene gli SKU dei pool multiruolo degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/multirolepools/usages/read | Ottiene gli utilizzi dei pool multiruolo degli ambienti di hosting. |
> | Azione | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Crea un nuovo pool front-end o ne aggiorna uno esistente in un ambiente del servizio app |
> | Azione | microsoft.web/hostingenvironments/operations/read | Ottiene le operazioni degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Ottiene gli endpoint di rete di tutte le dipendenze in uscita. |
> | Azione | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Approva connessioni a endpoint privato |
> | Azione | Microsoft.Web/hostingEnvironments/Read | Ottiene le proprietà di un ambiente del servizio app |
> | Azione | Microsoft.Web/hostingEnvironments/reboot/Action | Riavvia tutti i computer in un ambiente del servizio app |
> | Azione | microsoft.web/hostingenvironments/resume/action | Ripristina l'esecuzione degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/serverfarms/read | Ottiene i piani di servizio app degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/sites/read | Ottiene le app Web degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/suspend/action | Sospende l'esecuzione degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/usages/read | Ottiene gli utilizzi degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Ottiene le definizioni metrica dei pool di lavoro degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/workerpools/metrics/read | Ottiene la metrica dei pool di lavoro degli ambienti di hosting. |
> | Azione | Microsoft.Web/hostingEnvironments/workerPools/Read | Ottiene le proprietà di un pool di lavoro in un ambiente del servizio app |
> | Azione | microsoft.web/hostingenvironments/workerpools/skus/read | Ottiene gli SKU dei pool di lavoro degli ambienti di hosting. |
> | Azione | microsoft.web/hostingenvironments/workerpools/usages/read | Ottiene gli utilizzi dei pool di lavoro degli ambienti di hosting. |
> | Azione | Microsoft.Web/hostingEnvironments/workerPools/Write | Crea un nuovo pool di lavoro in un ambiente del servizio app o ne aggiorna uno esistente |
> | Azione | Microsoft.Web/hostingEnvironments/Write | Crea un nuovo ambiente del servizio app o ne aggiorna uno esistente |
> | Azione | microsoft.web/ishostingenvironmentnameavailable/read | Definisce se il nome dell'ambiente di hosting è disponibile. |
> | Azione | microsoft.web/ishostnameavailable/read | Verifica se il nome host è disponibile. |
> | Azione | microsoft.web/isusernameavailable/read | Verifica se il nome utente è disponibile. |
> | Azione | Microsoft.Web/listSitesAssignedToHostName/Read | Ottiene i nomi dei siti assegnati al nome host. |
> | Azione | microsoft.web/locations/apioperations/read | Ottiene le operazioni delle API dei percorsi. |
> | Azione | microsoft.web/locations/connectiongatewayinstallations/read | Ottiene le installazioni dei gateway di connessione dei percorsi. |
> | Azione | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Notifica l'eliminazione dei percorsi di rete virtuale o subnet. |
> | Azione | microsoft.web/locations/extractapidefinitionfromwsdl/action | Estrae la definizione dell'API da WSDL per le posizioni. |
> | Azione | microsoft.web/locations/listwsdlinterfaces/action | Elenca le interfacce WSDL per le posizioni. |
> | Azione | microsoft.web/locations/managedapis/apioperations/read | Ottiene le operazioni delle API gestite per le posizioni. |
> | Azione | Microsoft.Web/locations/managedapis/Join/Action | Aggiunge un'API gestita. |
> | Azione | microsoft.web/locations/managedapis/read | Ottiene le API gestite dei percorsi. |
> | Azione | Microsoft. Web/locations/operationResults/Read | Ottiene operazioni. |
> | Azione | Microsoft. Web/località/operazioni/lettura | Ottiene operazioni. |
> | Azione | microsoft.web/operations/read | Ottiene operazioni. |
> | Azione | microsoft.web/publishingusers/read | Ottiene gli utenti di pubblicazione. |
> | Azione | microsoft.web/publishingusers/write | Aggiorna gli utenti di pubblicazione. |
> | Azione | Microsoft.Web/recommendations/Read | Ottiene l'elenco di consigli per le sottoscrizioni. |
> | Azione | microsoft.web/register/action | Registra il provider di risorse Microsoft.Web per la sottoscrizione. |
> | Azione | microsoft.web/resourcehealthmetadata/read | Ottiene i metadati di Integrità risorse. |
> | Azione | microsoft.web/serverfarms/capabilities/read | Ottiene le capacità dei piani di servizio app. |
> | Azione | Microsoft.Web/serverfarms/Delete | Eliminare un piano di servizio app esistente |
> | Azione | Microsoft. Web/serverfarms/eventGridFilters/Delete | Elimina il filtro di griglia di eventi in server farm. |
> | Azione | Microsoft. Web/serverfarms/eventGridFilters/Read | Ottiene il filtro di griglia di eventi in server farm. |
> | Azione | Microsoft. Web/serverfarms/eventGridFilters/Write | Inserisci filtro griglia di eventi in server farm. |
> | Azione | microsoft.web/serverfarms/firstpartyapps/settings/delete | Elimina le impostazioni delle app proprietarie dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/firstpartyapps/settings/read | Ottiene le impostazioni delle app proprietarie dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/firstpartyapps/settings/write | Aggiorna le impostazioni delle app proprietarie dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Elimina gli inoltri di spazi dei nomi delle connessioni ibride per i piani di servizio app. |
> | Azione | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Ottiene gli inoltri di spazi dei nomi delle connessioni ibride per i piani di servizio app. |
> | Azione | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Ottiene le app Web degli inoltri spazio dei nomi connessione ibrida dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Ottiene i limiti del piano di connessione ibrida per i piani di servizio app. |
> | Azione | microsoft.web/serverfarms/hybridconnectionrelays/read | Ottiene gli inoltri connessione ibrida dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/metricdefinitions/read | Ottiene le definizioni metrica dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/metrics/read | Ottiene la metrica dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/operationresults/read | Ottiene i risultati dell'operazione per i piani di servizio app. |
> | Azione | Microsoft.Web/serverfarms/Read | Ottiene le proprietà per un piano di servizio app |
> | Azione | Microsoft.Web/serverfarms/restartSites/Action | Riavvia tutte le app Web in un piano di servizio app |
> | Azione | microsoft.web/serverfarms/sites/read | Ottiene le app Web dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/skus/read | Ottiene gli SKU dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/usages/read | Ottiene gli utilizzi dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Aggiorna i gateway delle connessioni rete virtuale dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/virtualnetworkconnections/read | Ottiene le connessioni rete virtuale dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Elimina i percorsi delle connessioni rete virtuale dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Ottiene i percorsi delle connessioni rete virtuale dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Aggiorna i percorsi delle connessioni rete virtuale dei piani di servizio app. |
> | Azione | microsoft.web/serverfarms/workers/reboot/action | Riavvia i ruoli di lavoro dei piani di servizio app. |
> | Azione | Microsoft.Web/serverfarms/Write | Crea un nuovo piano di servizio app o ne aggiorna uno esistente |
> | Azione | microsoft.web/sites/analyzecustomhostname/read | Analizza il nome host personalizzato. |
> | Azione | Microsoft.Web/sites/applySlotConfig/Action | Applica la configurazione slot dell'app Web dallo slot di destinazione all'app Web corrente |
> | Azione | Microsoft.Web/sites/backup/Action | Crea il backup di una nuova app Web |
> | Azione | microsoft.web/sites/backup/read | Ottiene il backup delle app Web. |
> | Azione | microsoft.web/sites/backup/write | Aggiorna il backup delle app Web. |
> | Azione | microsoft.web/sites/backups/action | Individua un backup esistente di app che può essere ripristinato da un BLOB nella risorsa di archiviazione di Azure. |
> | Azione | microsoft.web/sites/backups/delete | Elimina i backup delle app Web. |
> | Azione | microsoft.web/sites/backups/list/action | Elenca i backup delle app Web. |
> | Azione | Microsoft.Web/sites/backups/Read | Ottiene le proprietà del backup di un'app Web |
> | Azione | microsoft.web/sites/backups/restore/action | Ripristina i backup delle app Web. |
> | Azione | microsoft.web/sites/backups/write | Aggiorna i backup delle app Web. |
> | Azione | microsoft.web/sites/config/delete | Elimina la configurazione delle app Web. |
> | Azione | Microsoft.Web/sites/config/list/Action | Elenca le impostazioni dell'app Web sensibili alla sicurezza, ad esempio le credenziali di pubblicazione, le impostazioni dell'app e le stringhe di connessione |
> | Azione | Microsoft.Web/sites/config/Read | Ottiene le impostazioni di configurazione delle app Web |
> | Azione | microsoft.web/sites/config/snapshots/read | Ottenere gli snapshot di configurazione delle app Web. |
> | Azione | Microsoft.Web/sites/config/Write | Aggiorna le impostazioni di configurazione dell'app Web |
> | Azione | microsoft.web/sites/containerlogs/action | Ottiene i log dei contenitori compressi per l'app Web. |
> | Azione | microsoft.web/sites/containerlogs/download/action | Scarica i log dei contenitori di app Web. |
> | Azione | microsoft.web/sites/continuouswebjobs/delete | Elimina i processi Web continui delle app Web. |
> | Azione | microsoft.web/sites/continuouswebjobs/read | Ottiene i processi Web continui delle app Web. |
> | Azione | microsoft.web/sites/continuouswebjobs/start/action | Avvia i processi Web continui delle app Web. |
> | Azione | microsoft.web/sites/continuouswebjobs/stop/action | Interrompe i processi Web continui delle app Web. |
> | Azione | Microsoft.Web/sites/Delete | Eliminare un'app Web esistente |
> | Azione | microsoft.web/sites/deployments/delete | Elimina le distribuzioni delle app Web. |
> | Azione | microsoft.web/sites/deployments/log/read | Ottiene il registro delle distribuzioni delle app Web. |
> | Azione | microsoft.web/sites/deployments/read | Ottiene le distribuzioni delle app Web. |
> | Azione | microsoft.web/sites/deployments/write | Aggiorna le distribuzioni delle app Web. |
> | Azione | microsoft.web/sites/detectors/read | Ottiene i rilevamenti delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/analyses/execute/Action | Esegue l'analisi diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/analyses/read | Ottiene l'analisi diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/aspnetcore/read | Ottiene la diagnostica delle app Web per l'app ASP.NET Core. |
> | Azione | microsoft.web/sites/diagnostics/autoheal/read | Ottiene la funzione AutoHeal di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/deployment/read | Ottiene la distribuzione di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/deployments/read | Ottiene le distribuzioni di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/detectors/execute/Action | Esegue la funzione di rilevamento di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/detectors/read | Ottiene la funzione di rilevamento di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Ottiene le richieste non riuscite della diagnostica delle app Web per ogni URI. |
> | Azione | microsoft.web/sites/diagnostics/frebanalysis/read | Ottiene l'analisi FREB della diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/loganalyzer/read | Ottiene l'analizzatore dei log di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/read | Ottiene le categorie di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/runtimeavailability/read | Ottiene la disponibilità del runtime di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/servicehealth/read | Ottiene l'integrità di servizio della diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Ottiene l'analisi della CPU del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/sitecrashes/read | Ottiene gli arresti anomali del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/sitelatency/read | Ottiene la latenza del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Ottiene l'analisi della memoria del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Ottiene l'aggiornamento dell'impostazione di riavvio del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Ottiene il riavvio del sito di diagnostica delle app Web eseguito dall'utente. |
> | Azione | microsoft.web/sites/diagnostics/siteswap/read | Ottiene lo scambio del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/threadcount/read | Ottiene il conteggio dei thread del sito di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/workeravailability/read | Ottiene la disponibilità del processo di lavoro di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Ottiene il riciclo del processo di lavoro di diagnostica delle app Web. |
> | Azione | microsoft.web/sites/domainownershipidentifiers/read | Ottiene gli identificatori di proprietà del dominio delle app Web. |
> | Azione | microsoft.web/sites/domainownershipidentifiers/write | Aggiorna gli identificatori di proprietà del dominio delle app Web. |
> | Azione | Microsoft. Web/sites/eventGridFilters/Delete | Elimina il filtro di griglia di eventi nell'app Web. |
> | Azione | Microsoft. Web/sites/eventGridFilters/Read | Ottiene il filtro di griglia di eventi nell'app Web. |
> | Azione | Microsoft. Web/sites/eventGridFilters/Write | Inserire il filtro di griglia di eventi nell'app Web. |
> | Azione | microsoft.web/sites/extensions/delete | Elimina le estensioni del sito delle app Web. |
> | Azione | microsoft.web/sites/extensions/read | Ottiene le estensioni del sito delle app Web. |
> | Azione | microsoft.web/sites/extensions/write | Aggiorna le estensioni del sito delle app Web. |
> | Azione | microsoft.web/sites/functions/action | Funzioni delle app Web. |
> | Azione | microsoft.web/sites/functions/delete | Elimina funzioni delle app Web. |
> | Azione | Microsoft. Web/siti/funzioni/chiavi/Elimina | Elimina i tasti funzione. |
> | Azione | Microsoft. Web/sites/Functions/Keys/Write | Aggiornare i tasti funzione. |
> | Azione | Microsoft. Web/sites/Functions/listkeys/Action | Elenca i tasti funzione. |
> | Azione | microsoft.web/sites/functions/listsecrets/action | Elencare i segreti per le funzioni. |
> | Azione | microsoft.web/sites/functions/masterkey/read | Ottiene la chiave master delle funzioni delle app Web. |
> | Azione | Microsoft. Web/sites/Functions/Properties/Read | Leggere le proprietà delle funzioni delle app Web. |
> | Azione | Microsoft. Web/sites/Functions/Properties/Write | Aggiorna le proprietà delle funzioni delle app Web. |
> | Azione | microsoft.web/sites/functions/read | Ottiene le funzioni delle app Web. |
> | Azione | microsoft.web/sites/functions/token/read | Ottiene il token delle funzioni delle app Web. |
> | Azione | microsoft.web/sites/functions/write | Aggiorna le funzioni delle app Web. |
> | Azione | Microsoft. Web/sites/host/FunctionKeys/Delete | Elimina funzioni host tasti funzione. |
> | Azione | Microsoft. Web/sites/host/FunctionKeys/Write | Funzioni di aggiornamento host tasti funzione. |
> | Azione | Microsoft. Web/sites/host/listkeys/Action | Elenca le chiavi host di funzioni. |
> | Azione | Microsoft. Web/sites/host/listsyncstatus/Action | Stato trigger della funzione elenco di sincronizzazione. |
> | Azione | Microsoft. Web/sites/host/Properties/Read | Leggere le proprietà dell'host delle funzioni delle app Web. |
> | Azione | Microsoft. Web/sites/host/Sync/Action | Trigger della funzione Sync. |
> | Azione | Microsoft. Web/sites/host/systemkeys/Delete | Elimina le chiavi di sistema host di funzioni. |
> | Azione | Microsoft. Web/sites/host/systemkeys/Write | Funzioni di aggiornamento delle chiavi di sistema host. |
> | Azione | microsoft.web/sites/hostnamebindings/delete | Elimina i binding nome host delle app Web. |
> | Azione | microsoft.web/sites/hostnamebindings/read | Ottiene i binding nome host delle app Web. |
> | Azione | microsoft.web/sites/hostnamebindings/write | Aggiorna i binding nome host delle app Web. |
> | Azione | microsoft.web/sites/hostruntime/functions/keys/read | Ottiene le chiavi delle funzioni del runtime host di app Web. |
> | Azione | Microsoft.Web/sites/hostruntime/host/_master/read | Ottenere la chiave master di App per le funzioni per operazioni di amministrazione |
> | Azione | Microsoft.Web/sites/hostruntime/host/action | Eseguire azioni di runtime di App per le funzioni, come ad esempio trigger di sincronizzazione, aggiungere funzioni, richiamare funzioni, eliminare funzioni e così via. |
> | Azione | microsoft.web/sites/hostruntime/host/read | Ottiene l'host del runtime host di app Web. |
> | Azione | microsoft.web/sites/hybridconnection/delete | Elimina la connessione ibrida delle app Web. |
> | Azione | microsoft.web/sites/hybridconnection/read | Ottiene la connessione ibrida delle app Web. |
> | Azione | microsoft.web/sites/hybridconnection/write | Aggiorna la connessione ibrida delle app Web. |
> | Azione | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Elimina gli inoltri degli spazi dei nomi delle connessioni ibride delle app Web. |
> | Azione | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Elenca le chiavi degli inoltri degli spazi dei nomi delle connessioni ibride delle app Web. |
> | Azione | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Ottiene gli inoltri degli spazi dei nomi delle connessioni ibride delle app Web. |
> | Azione | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Aggiorna gli inoltri degli spazi dei nomi delle connessioni ibride delle app Web. |
> | Azione | microsoft.web/sites/hybridconnectionrelays/read | Ottiene i relay di connessione ibrida delle app Web. |
> | Azione | microsoft.web/sites/instances/deployments/delete | Elimina le distribuzioni delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/deployments/read | Ottiene le distribuzioni delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/extensions/log/read | Ottiene i log delle estensioni delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/extensions/processes/read | Ottiene i processi delle estensioni delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/extensions/read | Ottiene le estensioni delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/processes/delete | Elimina i processi delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/processes/modules/read | Ottiene i moduli dei processi delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/processes/read | Ottiene i processi delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/processes/threads/read | Ottiene i thread dei processi delle istanze delle app Web. |
> | Azione | microsoft.web/sites/instances/read | Ottiene le istanze delle app Web. |
> | Azione | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Stato di trigger della funzione di sincronizzazione degli elenchi. |
> | Azione | microsoft.web/sites/metricdefinitions/read | Ottiene le definizioni di metrica per le app Web. |
> | Azione | microsoft.web/sites/metrics/read | Ottiene la metrica di app Web. |
> | Azione | microsoft.web/sites/metricsdefinitions/read | Ottiene le definizioni di metrica per le app Web. |
> | Azione | microsoft.web/sites/migratemysql/action | Migra le app Web MySql. |
> | Azione | microsoft.web/sites/migratemysql/read | Ottiene la migrazione MySql delle app Web. |
> | Azione | microsoft.web/sites/networktrace/action | App Web di traccia della rete. |
> | Azione | microsoft.web/sites/networktraces/operationresults/read | Ottiene i risultati dell'operazione di traccia di rete per le app Web. |
> | Azione | microsoft.web/sites/newpassword/action | Consente di creare una nuova password per app Web. |
> | Azione | microsoft.web/sites/operationresults/read | Ottiene i risultati delle operazioni delle app Web. |
> | Azione | microsoft.web/sites/operations/read | Ottiene le operazioni delle app Web. |
> | Azione | microsoft.web/sites/perfcounters/read | Ottiene i contatori delle prestazioni delle app Web. |
> | Azione | microsoft.web/sites/premieraddons/delete | Elimina i componenti aggiuntivi Premier delle app Web. |
> | Azione | microsoft.web/sites/premieraddons/read | Ottiene i componenti aggiuntivi Premier delle app Web. |
> | Azione | microsoft.web/sites/premieraddons/write | Aggiorna i componenti aggiuntivi Premier delle app Web. |
> | Azione | microsoft.web/sites/privateaccess/read | Ottiene dati relativi all'abilitazione dell'accesso a siti privati e alle reti virtuali autorizzate che possono accedere al sito. |
> | Azione | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Approva connessioni a endpoint privato |
> | Azione | microsoft.web/sites/processes/modules/read | Ottiene i moduli dei processi delle app Web. |
> | Azione | microsoft.web/sites/processes/read | Ottiene i processi delle app Web. |
> | Azione | microsoft.web/sites/processes/threads/read | Get Web Apps elabora i thread. |
> | Azione | microsoft.web/sites/publiccertificates/delete | Elimina i certificati pubblici delle app Web. |
> | Azione | microsoft.web/sites/publiccertificates/read | Ottiene i certificati pubblici delle app Web. |
> | Azione | microsoft.web/sites/publiccertificates/write | Aggiorna i certificati pubblici delle app Web. |
> | Azione | Microsoft.Web/sites/publish/Action | Pubblica un'app Web |
> | Azione | Microsoft.Web/sites/publishxml/Action | Ottiene l'XML del profilo di pubblicazione di un'app Web |
> | Azione | microsoft.web/sites/publishxml/read | Ottiene l'XML di pubblicazione delle app Web. |
> | Azione | Microsoft.Web/sites/Read | Ottiene le proprietà di un'app Web |
> | Azione | microsoft.web/sites/recommendationhistory/read | Ottiene la cronologia consigli delle app Web. |
> | Azione | microsoft.web/sites/recommendations/disable/action | Disattiva i consigli delle app Web. |
> | Azione | Microsoft.Web/sites/recommendations/Read | Ottiene l'elenco dei consigli per l'app Web. |
> | Azione | microsoft.web/sites/recover/action | Recupera le app Web. |
> | Azione | Microsoft.Web/sites/resetSlotConfig/Action | Ripristina la configurazione dell'app Web |
> | Azione | microsoft.web/sites/resourcehealthmetadata/read | Ottiene i metadati di Integrità risorse delle app Web. |
> | Azione | Microsoft.Web/sites/restart/Action | Riavvia un'app Web |
> | Azione | microsoft.web/sites/restore/read | Ottiene il ripristino delle app Web. |
> | Azione | microsoft.web/sites/restore/write | Ripristina le app Web. |
> | Azione | microsoft.web/sites/restorefrombackupblob/action | Ripristina l'app Web dal BLOB di Backup. |
> | Azione | Microsoft. Web/sites/restorefromdeletedapp/Action | Ripristina le app Web dall'app eliminata. |
> | Azione | microsoft.web/sites/restoresnapshot/action | Ripristina gli snapshot delle app Web. |
> | Azione | microsoft.web/sites/siteextensions/delete | Elimina le estensioni del sito delle app Web. |
> | Azione | microsoft.web/sites/siteextensions/read | Ottiene le estensioni del sito delle app Web. |
> | Azione | microsoft.web/sites/siteextensions/write | Aggiorna le estensioni del sito delle app Web. |
> | Azione | microsoft.web/sites/slots/analyzecustomhostname/read | Ottiene l'analisi nome host personalizzato degli slot per le app Web. |
> | Azione | Microsoft.Web/sites/slots/applySlotConfig/Action | Applica la configurazione slot dell'app Web dallo slot di destinazione allo slot corrente. |
> | Azione | Microsoft.Web/sites/slots/backup/Action | Crea un nuovo backup di slot di app Web. |
> | Azione | microsoft.web/sites/slots/backup/read | Ottiene il backup degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/backup/write | Aggiorna il backup degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/backups/action | Individua i backup degli slot di App Web. |
> | Azione | microsoft.web/sites/slots/backups/delete | Elimina i backup degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/backups/list/action | Elenca i backup degli slot per le app Web. |
> | Azione | Microsoft.Web/sites/slots/backups/Read | Ottiene le proprietà del backup di uno slot delle app Web |
> | Azione | microsoft.web/sites/slots/backups/restore/action | Ripristina i backup degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/config/delete | Elimina la configurazione degli slot per le app Web. |
> | Azione | Microsoft.Web/sites/slots/config/list/Action | Elenca le impostazioni importanti per la sicurezza degli slot per le app Web, quali le credenziali di pubblicazione, le impostazioni delle app e le stringhe di connessione |
> | Azione | Microsoft.Web/sites/slots/config/Read | Ottiene le impostazioni di configurazione degli slot per le app Web |
> | Azione | Microsoft.Web/sites/slots/config/Write | Aggiorna le impostazioni di configurazione degli slot per le app Web |
> | Azione | microsoft.web/sites/slots/containerlogs/action | Ottiene i log dei contenitori compressi per gli slot di App Web. |
> | Azione | microsoft.web/sites/slots/containerlogs/download/action | Scarica i log dei contenitori degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/continuouswebjobs/delete | Elimina i processi Web continui degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/continuouswebjobs/read | Ottiene i processi Web continui degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/continuouswebjobs/start/action | Avvia i processi Web continui degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/continuouswebjobs/stop/action | Arresta i processi Web continui degli slot per le app Web. |
> | Azione | Microsoft.Web/sites/slots/Delete | Elimina uno slot di app Web esistente |
> | Azione | microsoft.web/sites/slots/deployments/delete | Elimina le distribuzioni degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/deployments/log/read | Ottiene il registro delle distribuzioni degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/deployments/read | Ottiene le distribuzioni degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/deployments/write | Aggiorna le distribuzioni degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/detectors/read | Ottenere funzionalità di rilevamento degli slot di App Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Esegue l'analisi diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/analyses/read | Ottiene l'analisi diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Ottiene la diagnostica degli slot delle app Web per l'app ASP.NET Core. |
> | Azione | microsoft.web/sites/slots/diagnostics/autoheal/read | Ottiene la funzione AutoHeal di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/deployment/read | Ottiene la distribuzione di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/deployments/read | Ottiene le distribuzioni di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Esegue la funzione di rilevamento di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/detectors/read | Ottiene la funzione di rilevamento di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Ottiene l'analisi FREB della diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Ottiene l'analisi dei log di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/read | Ottiene la diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Ottiene la disponibilità del runtime di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/servicehealth/read | Ottiene l'integrità di servizio di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Ottiene l'analisi della CPU del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Ottiene gli arresti anomali del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/sitelatency/read | Ottiene la latenza del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Ottiene l'analisi della memoria del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Ottiene l'aggiornamento dell'impostazione di riavvio del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Ottiene il riavvio del sito di diagnostica degli slot delle app Web eseguito dall'utente. |
> | Azione | microsoft.web/sites/slots/diagnostics/siteswap/read | Ottiene lo scambio del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/threadcount/read | Ottiene il conteggio dei thread del sito di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/workeravailability/read | Ottiene la disponibilità del processo di lavoro di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Ottiene il riciclo del processo di lavoro di diagnostica degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/domainownershipidentifiers/read | Ottiene gli identificatori di proprietà del dominio degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/functions/listsecrets/action | Elenca i segreti delle funzioni degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/functions/read | Ottiene le funzioni degli slot di App Web. |
> | Azione | microsoft.web/sites/slots/hostnamebindings/delete | Elimina le associazioni nome host degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/hostnamebindings/read | Ottiene le associazioni nome host degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/hostnamebindings/write | Aggiorna le associazioni nome host degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/hybridconnection/delete | Elimina la connessione ibrida degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/hybridconnection/read | Ottiene la connessione ibrida degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/hybridconnection/write | Aggiorna la connessione ibrida degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Elimina gli inoltri degli spazi dei nomi delle connessioni ibride degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Aggiorna gli inoltri degli spazi dei nomi delle connessioni ibride degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/hybridconnectionrelays/read | Ottiene gli inoltri della connessione ibrida degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/instances/deployments/read | Ottiene le distribuzioni delle istanze degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/instances/processes/delete | Elimina i processi delle istanze degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/instances/processes/read | Ottiene i processi delle istanze degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/instances/read | Ottiene le istanze degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/metricdefinitions/read | Ottiene le definizioni metrica degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/metrics/read | Ottiene la metrica degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/migratemysql/read | Ottiene la migrazione MySql degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/networktrace/action | Analisi di rete degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/networktraces/operationresults/read | Ottiene i risultati dell'operazione di traccia di rete degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/newpassword/action | Consente di creare una nuova password per slot di app Web. |
> | Azione | microsoft.web/sites/slots/operationresults/read | Ottiene i risultati delle operazioni degli slot di app Web. |
> | Azione | microsoft.web/sites/slots/operations/read | Ottiene le operazioni degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/perfcounters/read | Ottiene i contatori delle prestazioni degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/phplogging/read | Ottiene la registrazione PHP degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/premieraddons/delete | Elimina i componenti aggiuntivi Premier degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/premieraddons/read | Ottiene i componenti aggiuntivi Premier degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/premieraddons/write | Aggiorna i componenti aggiuntivi Premier degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/processes/read | Ottiene i processi degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/publiccertificates/delete | Elimina i certificati pubblici degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/publiccertificates/read | Ottiene i certificati pubblici degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/publiccertificates/write | Crea o aggiorna i certificati pubblici degli slot delle app Web. |
> | Azione | Microsoft.Web/sites/slots/publish/Action | Pubblica uno slot di app Web |
> | Azione | Microsoft.Web/sites/slots/publishxml/Action | Ottiene l'XML del profilo di pubblicazione per uno slot di app Web |
> | Azione | Microsoft.Web/sites/slots/Read | Ottiene le proprietà di uno slot di distribuzione di app Web |
> | Azione | microsoft.web/sites/slots/recover/action | Ripristina gli slot delle app Web. |
> | Azione | Microsoft.Web/sites/slots/resetSlotConfig/Action | Ripristina la configurazione dello slot di app Web |
> | Azione | microsoft.web/sites/slots/resourcehealthmetadata/read | Ottiene i metadati di Integrità risorse degli slot delle app Web. |
> | Azione | Microsoft.Web/sites/slots/restart/Action | Riavvia uno slot di app Web |
> | Azione | microsoft.web/sites/slots/restore/read | Ottiene il ripristino degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/restore/write | Ripristina gli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/restorefrombackupblob/action | Ripristinare lo slot di App Web dal BLOB di Backup. |
> | Azione | Microsoft. Web/sites/Slots/restorefromdeletedapp/Action | Ripristina gli slot dell'app Web dall'app eliminata. |
> | Azione | microsoft.web/sites/slots/restoresnapshot/action | Ripristina gli snapshot degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/siteextensions/delete | Elimina le estensioni del sito degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/siteextensions/read | Ottiene le estensioni del sito degli slot delle app Web. |
> | Azione | microsoft.web/sites/slots/siteextensions/write | Aggiorna le estensioni del sito degli slot delle app Web. |
> | Azione | Microsoft.Web/sites/slots/slotsdiffs/Action | Ottiene le differenze di configurazione tra l'app Web e gli slot |
> | Azione | Microsoft.Web/sites/slots/slotsswap/Action | Scambia gli slot di distribuzione di un'app Web |
> | Azione | microsoft.web/sites/slots/snapshots/read | Ottiene gli snapshot degli slot delle app Web. |
> | Azione | Microsoft.Web/sites/slots/sourcecontrols/Delete | Elimina le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web |
> | Azione | Microsoft.Web/sites/slots/sourcecontrols/Read | Ottiene le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web |
> | Azione | Microsoft.Web/sites/slots/sourcecontrols/Write | Aggiorna le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web |
> | Azione | Microsoft.Web/sites/slots/start/Action | Avvia uno slot di app Web |
> | Azione | Microsoft.Web/sites/slots/stop/Action | Arresta uno slot di app Web |
> | Azione | microsoft.web/sites/slots/sync/action | Sincronizza gli slot di app Web. |
> | Azione | microsoft.web/sites/slots/triggeredwebjobs/delete | Elimina i processi Web attivati degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/triggeredwebjobs/read | Ottiene i processi Web attivati degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/triggeredwebjobs/run/action | Esegue i processi Web attivati degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/usages/read | Ottiene gli utilizzi degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/virtualnetworkconnections/delete | Elimina le connessioni rete virtuale degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Aggiorna i gateway delle connessioni rete virtuale degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/virtualnetworkconnections/read | Ottiene le connessioni rete virtuale degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/virtualnetworkconnections/write | Aggiorna le connessioni rete virtuale degli slot per le app Web. |
> | Azione | microsoft.web/sites/slots/webjobs/read | Ottiene i processi Web degli slot per le app Web. |
> | Azione | Microsoft.Web/sites/slots/Write | Crea un nuovo slot di app Web o ne aggiorna uno esistente |
> | Azione | Microsoft.Web/sites/slotsdiffs/Action | Ottiene le differenze di configurazione tra l'app Web e gli slot |
> | Azione | Microsoft.Web/sites/slotsswap/Action | Scambia gli slot di distribuzione di un'app Web |
> | Azione | microsoft.web/sites/snapshots/read | Ottiene snapshot delle app Web. |
> | Azione | Microsoft.Web/sites/sourcecontrols/Delete | Elimina le impostazioni di configurazione del controllo codice sorgente dell'app Web |
> | Azione | Microsoft.Web/sites/sourcecontrols/Read | Ottiene le impostazioni di configurazione del controllo codice sorgente dell'app Web |
> | Azione | Microsoft.Web/sites/sourcecontrols/Write | Aggiorna le impostazioni di configurazione del controllo codice sorgente dell'app Web |
> | Azione | Microsoft.Web/sites/start/Action | Avvia un'app Web |
> | Azione | Microsoft.Web/sites/stop/Action | Arresta un'app Web |
> | Azione | microsoft.web/sites/sync/action | App Web di sincronizzazione. |
> | Azione | microsoft.web/sites/syncfunctiontriggers/action | Trigger della funzione Sync. |
> | Azione | microsoft.web/sites/triggeredwebjobs/delete | Elimina i processi Web attivati delle app Web. |
> | Azione | microsoft.web/sites/triggeredwebjobs/history/read | Ottiene la cronologia dei processi Web attivati delle app Web. |
> | Azione | microsoft.web/sites/triggeredwebjobs/read | Ottiene i processi Web attivati delle app Web. |
> | Azione | microsoft.web/sites/triggeredwebjobs/run/action | Esegue i processi Web attivati delle app Web. |
> | Azione | microsoft.web/sites/usages/read | Ottiene gli utilizzi delle app Web. |
> | Azione | microsoft.web/sites/virtualnetworkconnections/delete | Elimina le connessioni di rete virtuale delle app Web. |
> | Azione | microsoft.web/sites/virtualnetworkconnections/gateways/read | Ottiene i gateway delle connessioni di rete virtuale delle app Web. |
> | Azione | microsoft.web/sites/virtualnetworkconnections/gateways/write | Aggiorna i gateway delle connessioni di rete virtuale delle app Web. |
> | Azione | microsoft.web/sites/virtualnetworkconnections/read | Ottiene le connessioni di rete virtuale delle app Web. |
> | Azione | microsoft.web/sites/virtualnetworkconnections/write | Aggiorna le connessioni di rete virtuale delle app Web. |
> | Azione | microsoft.web/sites/webjobs/read | Ottiene i processi Web delle app Web. |
> | Azione | Microsoft.Web/sites/Write | Crea una nuova app Web o ne aggiorna una esistente |
> | Azione | microsoft.web/skus/read | Ottiene SKU. |
> | Azione | microsoft.web/sourcecontrols/read | Ottiene i controlli del codice sorgente. |
> | Azione | microsoft.web/sourcecontrols/write | Aggiorna i controlli del codice sorgente. |
> | Azione | microsoft.web/unregister/action | Annulla la registrazione del provider di risorse Microsoft.Web per la sottoscrizione. |
> | Azione | microsoft.web/validate/action | Convalida il provider di risorse Microsoft.Web per la sottoscrizione. |
> | Azione | microsoft.web/verifyhostingenvironmentvnet/action | Verifica la rete virtuale dell'ambiente di hosting. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo di azione | Operazione | Description |
> | --- | --- | --- |
> | Azione | Microsoft.WorkloadMonitor/components/read | Ottiene i componenti per la risorsa |
> | Azione | Microsoft.WorkloadMonitor/componentsSummary/read | Ottiene il riepilogo dei componenti |
> | Azione | Microsoft.WorkloadMonitor/monitorInstances/read | Ottiene le istanze dei monitoraggi per la risorsa |
> | Azione | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Ottiene un riepilogo delle istanze di monitoraggio |
> | Azione | Microsoft.WorkloadMonitor/monitors/read | Ottiene i monitoraggi per la risorsa |
> | Azione | Microsoft.WorkloadMonitor/monitors/write | Configura il monitoraggio per la risorsa |
> | Azione | Microsoft.WorkloadMonitor/notificationSettings/read | Ottiene le impostazioni di notifica per la risorsa |
> | Azione | Microsoft.WorkloadMonitor/notificationSettings/write | Configura le impostazioni di notifica per la risorsa |
> | Azione | Microsoft.WorkloadMonitor/operations/read | Ottiene le operazioni supportate |

## <a name="next-steps"></a>Passaggi successivi

- [Associare il provider di risorse al servizio](../azure-resource-manager/azure-services-resource-providers.md)
- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Ruoli predefiniti per le risorse di Azure](built-in-roles.md)
