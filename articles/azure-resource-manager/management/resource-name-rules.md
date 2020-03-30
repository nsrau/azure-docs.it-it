---
title: Restrizioni di denominazione delle risorse
description: Mostra le regole e le restrizioni per la denominazione delle risorse di Azure.Shows the rules and restrictions for naming Azure resources.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 580b7efd26d5729baf236b59452f63483c003e44
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366227"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regole di denominazione e restrizioni per le risorse di Azure

Questo articolo riepiloga le regole di denominazione e le restrizioni per le risorse di Azure.This article summarizes naming rules and restrictions for Azure resources. Per suggerimenti su come assegnare un nome alle risorse, vedere [Pronto: Convenzioni di denominazione e assegnazione dei tag consigliate](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Per i nomi delle risorse non viene fatta distinzione tra maiuscole e minuscole, a meno che non venga specificato in modo specifico nella colonna dei caratteri validi.

Nelle tabelle seguenti, il termine alfanumerico si riferisce a:

* **da a** a **z** (lettere minuscole)
* **Dalla A** alla **z** (lettere maiuscole)
* **Da 0** a **9** (numeri)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | gruppo di risorse | 3-63 | Lettere e numeri minuscoli.<br><br>Iniziare con la lettera minuscola. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | service | global | 1-50 | Alfanumerici.<br><br>Inizia con la lettera. |
> | servizio / apis | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / problemi | api | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / problemi / allegati | Problema | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / problemi / commenti | Problema | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / operazioni | api | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / operazioni / tag | operazione | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / rilasci | api | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Iniziare e terminare con caratteri alfanumerici o di sottolineatura. |
> | servizio / apis / schemi | api | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / tagDescriptions | api | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / apis / tag | api | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | service / api-version-sets | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / autorizzazioneServer | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / backend | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / certificati | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / diagnostica | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio/ gruppi | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / gruppi / utenti | group | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | service / identityProviders | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / logger | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / notifiche | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / notifiche / destinatarioE-mail | notifica | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / openidConnectProviders | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / politiche | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / prodotti | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / prodotti / apis | product | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / prodotti / gruppi | product | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / prodotti / tag | product | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / proprietà | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / abbonamenti | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / tag | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / modelli | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |
> | servizio / utenti | service | 1-256 | Non è possibile utilizzare:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration (Configurazione applicazioni)

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | configurationStores | gruppo di risorse | 5-50 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | locks | ambito di assegnazione | 1-90 | Alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi.<br><br>Non posso finire nel periodo. |
> | policyassignments | ambito di assegnazione | 1-128 nome visualizzato<br><br>1-260 nome risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della `%` risorsa non può includere e non può terminare con punto o spazio. |
> | Policydefinitions | ambito di definizione | 1-128 nome visualizzato<br><br>1-260 nome risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della `%` risorsa non può includere e non può terminare con punto o spazio. |
> | policySetDefinitions | ambito di definizione | 1-128 nome visualizzato<br><br>1-260 nome risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della `%` risorsa non può includere e non può terminare con punto o spazio.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | automationAccounts | gruppo di risorse | 6-50 | Alfanumerici e trattini.<br><br>Iniziare con la lettera e terminare con alfanumerico. |
> | automationAccount / certificati | account di automazione | 1-128 | Non è possibile utilizzare:<br> `<>*%&:\?.+/` <br><br>Non si può finire con lo spazio.  |
> | automationAccount / connessioni | account di automazione | 1-128 | Non è possibile utilizzare:<br> `<>*%&:\?.+/` <br><br>Non si può finire con lo spazio. |
> | automationAccount / credenziali | account di automazione | 1-128 | Non è possibile utilizzare:<br> `<>*%&:\?.+/` <br><br>Non si può finire con lo spazio. |
> | automationAccount / runbook | account di automazione | 1-63 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Inizia con la lettera.  |
> | automationAccount / abachi | account di automazione | 1-128 | Non è possibile utilizzare:<br> `<>*%&:\?.+/` <br><br>Non si può finire con lo spazio. |
> | automationAccount / variabili | account di automazione | 1-128 | Non è possibile utilizzare:<br> `<>*%&:\?.+/` <br><br>Non si può finire con lo spazio. |
> | automationAccounts / osservatori | account di automazione | 1-63 |  Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Inizia con la lettera. |
> | automationAccount / webhook | account di automazione | 1-128 | Non è possibile utilizzare:<br> `<>*%&:\?.+/` <br><br>Non si può finire con lo spazio. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Lettere e numeri minuscoli. |
> | batchAccounts / applicazioni | conto batch | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | batchAccounts / certificati | conto batch | 5-45 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | batchAccounts / pool | conto batch | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Lettere e numeri minuscoli.<br><br>Iniziare con la lettera minuscola. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. |
> | botServices / canali | servizio bot | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. |
> | botServices / Connessioni | servizio bot | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. |
> | enterpriseChannels | gruppo di risorse | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. Trattini consecutivi non consentiti. |
> | Redis / firewallRegole | Redis | 1-256 | Alfanumerici |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Profili | gruppo di risorse | 1-260 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |
> | profili/endpoint | global | 1-50 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | certificateOrders | gruppo di risorse | 3-30 | Alfanumerici. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | gruppo di risorse | 2-64 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | availabilitySets | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Terminare con caratteri alfanumerici o di sottolineatura. |
> | DiskEncryptionSets (Crittografia disk) | gruppo di risorse | 1-80 | Caratteri alfanumerici e caratteri di sottolineatura. |
> | disks | gruppo di risorse | 1-80 | Caratteri alfanumerici e caratteri di sottolineatura. |
> | galleries | gruppo di risorse | 1-80 | Punti e punti alfanumerici.<br><br>Iniziare e terminare con alfanumerici. |
> | gallerie / applicazioni | raccolta | 1-80 | Alfanumerici, trattini e punti.<br><br>Iniziare e terminare con alfanumerici. |
> | gallerie / applicazioni / versioni | application | Intero a 32 bit | Numeri e punti. |
> | gallerie / immagini | raccolta | 1-80 | Alfanumerici, trattini e punti.<br><br>Iniziare e terminare con alfanumerici. |
> | gallerie / immagini / versioni | image | Intero a 32 bit | Numeri e punti. |
> | images | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Terminare con caratteri alfanumerici o di sottolineatura. |
> | snapshots | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Terminare con caratteri alfanumerici o di sottolineatura. |
> | virtualMachines | gruppo di risorse | 1-15 (Windows)<br>1-64 (Linux)<br><br>Vedere la nota seguente. | Non è possibile utilizzare:<br> `\/""[]:|<>+=;,?*@&`<br><br>Non posso iniziare con il punteggio di sottolineatura. Non può terminare con punto o trattino. |
> | virtualMachineScaleSets | gruppo di risorse | 1-15 (Windows)<br>1-64 (Linux)<br><br>Vedere la nota seguente. | Non è possibile utilizzare:<br> `\/""[]:|<>+=;,?*@&`<br><br>Non posso iniziare con il punteggio di sottolineatura. Non può terminare con punto o trattino. |

> [!NOTE]
> Le macchine virtuali di Azure hanno due nomi distinti: nome della risorsa e nome host. Quando si crea una macchina virtuale nel portale, lo stesso valore viene utilizzato per entrambi i nomi. Le restrizioni nella tabella precedente riguardano il nome host. Il nome della risorsa effettivo può avere al massimo 64 caratteri.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | containerGroups | gruppo di risorse | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. Non sono consentiti trattini consecutivi. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | registries | global | 5-50 | Alfanumerici. |
> | registri / buildTasks | Registro di sistema | 5-50 | Alfanumerici. |
> | registri / buildTasks/steps | attività di compilazione | 5-50 | Alfanumerici. |
> | registri / repliche | Registro di sistema | 5-50 | Alfanumerici. |
> | registri / scopeMappe | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registri / attività | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registri / token | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registri / webhooks | Registro di sistema | 5-50 | Alfanumerici. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managedClusters | gruppo di risorse | 1-63 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Iniziare e terminare con alfanumerici. |
> | openShiftManagedClusters | gruppo di risorse | 1-30 | Alfanumerici. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | hubs | gruppo di risorse | 1-64 | Alfanumerici.<br><br>Inizia con la lettera.  |
> | hub/ authorizationPolicies | hub | 1-50 | Caratteri alfanumerici, caratteri di sottolineatura e punti.<br><br>Iniziare e terminare con alfanumerici. |
> | hub/connettori | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub/connettori/mapping | connettore | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub/ interazioni | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / kpi | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / collegamenti | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / previsioni | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / profili | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / relazioneLink | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / relazioni | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub / roleAssignments | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | hub/viste | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders (informazioni in base ai provider personalizzati)

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | associazioni | gruppo di risorse | 1-180 | Non è possibile utilizzare:<br>`%&\\?/`<br><br>Non può finire con punto o spazio. |
> | resourceProviders | gruppo di risorse | 3-64 | Non è possibile utilizzare:<br>`%&\\?/`<br><br>Non può finire con punto o spazio. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | jobs | gruppo di risorse | 3-24 | Alfanumerici, trattini, caratteri di sottolineatura e punti. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | aree di lavoro | gruppo di risorse | 3-30 | Caratteri alfanumerici, caratteri di sottolineatura e trattini |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | factories | global | 3-63 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |
> | fabbriche / flussi di dati | factory | 1-260 | Non è possibile utilizzare:<br>`<>*#.%&:\\+?/`<br><br>Iniziare con alfanumerico. |
> | fabbriche / set di dati | factory | 1-260 | Non è possibile utilizzare:<br>`<>*#.%&:\\+?/`<br><br>Iniziare con alfanumerico. |
> | factory / integrationRuntimes | factory | 3-63 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |
> | fabbriche / linkedservices | factory | 1-260 | Non è possibile utilizzare:<br>`<>*#.%&:\\+?/`<br><br>Iniziare con alfanumerico. |
> | fabbriche / condutture | factory | 1-260 | Non è possibile utilizzare:<br>`<>*#.%&:\\+?/`<br><br>Iniziare con alfanumerico. |
> | fabbriche / trigger | factory | 1-260 | Non è possibile utilizzare:<br>`<>*#.%&:\\+?/`<br><br>Iniziare con alfanumerico. |
> | factory / trigger / rerunTriggers | trigger | 1-260 | Non è possibile utilizzare:<br>`<>*#.%&:\\+?/`<br><br>Iniziare con alfanumerico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | global | 3-24 | Lettere e numeri minuscoli. |
> | account / computePolicies | account | 3-60 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | account / dataLakeStoreAccounts | account | 3-24 | Lettere e numeri minuscoli. |
> | account / firewallRegole | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | account / storageAccounts | account | 3-60 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | global | 3-24 | Lettere e numeri minuscoli. |
> | account / firewallRegole | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | account / virtualNetworkRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | services | gruppo di risorse | 2-62 | Alfanumerici, trattini, punti e caratteri di sottolineatura.<br><br>Iniziare con alfanumerico. |
> | servizi/ progetti | service | 2-57 | Alfanumerici, trattini, punti e caratteri di sottolineatura.<br><br>Iniziare con alfanumerico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | server / database | servers | 1-63 | Alfanumerici e trattini. |
> | server / firewallRegole | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | server / virtualNetworkRules | servers | 1-128 | Alfanumerici e trattini. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | server / database | servers | 1-63 | Alfanumerici e trattini. |
> | server / firewallRegole | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | server / virtualNetworkRules | servers | 1-128 | Alfanumerici e trattini. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | server / database | servers | 1-63 | Alfanumerici e trattini. |
> | server / firewallRegole | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | server / virtualNetworkRules | servers | 1-128 | Alfanumerici e trattini. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Hub IoT | global | 3-50 | Alfanumerici e trattini.<br><br>Non può terminare con il trattino. |
> | IotHubs / certificati | Hub IoT | 1-64 | Alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventiHubEndpoint | 1-50 | Alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | provisioningServizi | gruppo di risorse | 3-64 | Alfanumerici e trattini.<br><br>Terminare con alfanumerico. |
> | provisioningServices /certificati | provisioningServizi | 1-64 | Alfanumerici, trattini, punti e caratteri di sottolineatura. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | labs | gruppo di risorse | 1-50 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | laboratori / customimages | Laboratorio | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, trattini e parentesi. |
> | laboratori / formule | Laboratorio | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, trattini e parentesi. |
> | laboratori / macchine virtuali | Laboratorio | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. Non possono essere tutti numeri. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | Lettere minuscole, numeri e trattini.<br><br>Iniziare con una lettera o un numero minuscolo. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | domains | gruppo di risorse | 3-50 | Alfanumerici e trattini. |
> | domini / argomenti | dominio | 3-50 | Alfanumerici e trattini. |
> | eventSubscriptions | gruppo di risorse | 3-64 | Alfanumerici e trattini. |
> | topics | gruppo di risorse | 3-50 | Alfanumerici e trattini. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | gruppo di risorse | 6-50 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con la lettera o il numero. |
> | spazi dei nomi | global | 6-50 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con la lettera o il numero. |
> | spazi dei nomi / AuthorizationRules | namespace | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con la lettera o il numero. |
> | spazi dei nomi / disasterRecoveryConfigs | namespace | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con la lettera o il numero. |
> | spazi dei nomi / eventhubs | namespace | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con la lettera o il numero. |
> | spazi dei nomi / eventhubs / authorizationRules | hub eventi | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con la lettera o il numero. |
> | spazi dei nomi / eventhubs / consumergroups | hub eventi | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con la lettera o il numero. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | global | 3-59 | Alfanumerici e trattini<br><br>Iniziare e terminare con la lettera o il numero. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | jobs | gruppo di risorse | 2-64 | Alfanumerici e trattini.<br><br>Inizia con la lettera. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | components | gruppo di risorse | 1-260 | Non è possibile utilizzare:<br>`%&\?/` <br><br>Non può finire con spazio o punto.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Lettere minuscole, numeri e trattini.<br><br>Iniziare con una lettera o un numero minuscolo. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | insiemi di credenziali | global | 3-24 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con lettera o cifra. Non può contenere trattini consecutivi. |
> | volte / segreti | Insiemi di credenziali | 1-127 | Alfanumerici e trattini. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | global | 4-22 | Lettere e numeri minuscoli.<br><br>Inizia con la lettera. |
> | /cluster/ database | cluster | 1-260 | Alfanumerici, trattini, spazi e punti. |
> | /clusters / database / dataConnections | database | 1-40 | Alfanumerici, trattini, spazi e punti. |
> | /clusters / database / eventhubconnections | database | 1-40 | Alfanumerici, trattini, spazi e punti. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | integrationAccounts | gruppo di risorse | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts / assembly | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts / batchConfigurations | Connettori | 1-20 | Alfanumerici. |
> | integrationAccounts / certificati | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrazioneConti / mappe | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrazioneAccount / partner | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccount / rosettanetprocessconfigurations | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts / schemi | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts / sessioni | Connettori | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAmbienti di servizio | gruppo di risorse | 1-80 | Alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | integrationServiceEnvironments / managedApis | ambiente del servizio di integrazione | 1-80 | Alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | flussi di lavoro | gruppo di risorse | 1-80 | Alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | commitmentPlans | gruppo di risorse | 1-260 | Non è possibile utilizzare:<br>`<>*%&:?+/\\`<br><br>Non si può finire con uno spazio. |
> | webServices | gruppo di risorse | 1-260 | Non è possibile utilizzare:<br>`<>*%&:?+/\\`<br><br>Non si può finire con uno spazio. |
> | aree di lavoro | gruppo di risorse | 1-260 | Non è possibile utilizzare:<br>`<>*%&:?+/\\`<br><br>Non si può finire con uno spazio. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | aree di lavoro | gruppo di risorse | 3-33 | Alfanumerici e trattini. |
> | aree di lavoro/ calcola | area di lavoro | 2-16 | Alfanumerici e trattini. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | userAssignedIdentities | gruppo di risorse | 3-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura<br><br>Iniziare con la lettera o il numero. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | gruppo di risorse | 1-98 (per il nome e il nome dell'account del gruppo di risorse) | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | mediaservices | gruppo di risorse | 3-24 | Lettere e numeri minuscoli. |
> | mediaservices / liveEvents | Servizio multimediale | 1-32 | Alfanumerici e trattini.<br><br>Iniziare con alfanumerico. |
> | mediaservices / liveEvents / liveOutputs | Evento dal vivo | 1-256 | Alfanumerici e trattini.<br><br>Iniziare con alfanumerico. |
> | mediaservices / streamingEndpoints | Servizio multimediale | 1-24 | Alfanumerici e trattini.<br><br>Iniziare con alfanumerico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | applicationGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | applicationSecurityGroups | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | azureFirewalls | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Terminare con caratteri alfanumerici o di sottolineatura. |
> | bastionGli ospiti | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | connections | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | dns-ones | gruppo di risorse | 1-63 caratteri<br><br>Da 2 a 34 etichette<br><br>Ogni etichetta è un set di caratteri separati da un punto. Ad esempio, **contoso.com** ha 2 etichette. | Ogni etichetta può contenere caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Ogni etichetta è separata da un punto. |
> | expressRouteCircuits | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | firewallPolicies (Criteri firewall) | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | firewallPolicies / ruleGroups | criteri firewall | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | frontDoors | global | 5-64 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |
> | loadBalancers | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | loadBalancers / inboundNatRules | bilanciamento del carico | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | localNetworkGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | networkInterfaces | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | networkSecurityGroups | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | networkSecurityGroups / securityRules | gruppo di sicurezza di rete | 1-80 |  Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | networkWatchers | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | privateDns- | gruppo di risorse | 1-63 caratteri<br><br>Da 2 a 34 etichette<br><br>Ogni etichetta è un set di caratteri separati da un punto. Ad esempio, **contoso.com** ha 2 etichette. | Ogni etichetta può contenere caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Ogni etichetta è separata da un punto. |
> | privateDns - virtualNetworkLinks | zona DNS privata | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | publicIPAddresses | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | publicIPPrefixes | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | routeFilters | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | routeFilters / routeFilterRules | filtro della route | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | routeTables | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | routeTables / route | Tabella di route | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | serviceEndpointPolicies | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | trafficmanagerprofiles | global | 1-63 | Alfanumerici, trattini e punti.<br><br>Iniziare e terminare con alfanumerici. |
> | virtualNetworkGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | virtualNetworks | gruppo di risorse | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | virtualnetworks/subnet | rete virtuale | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | virtualNetworks / virtualNetworkPeerings | rete virtuale | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | virtualWans | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | vpnGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | vpnGateway / vpnConnections | gateway VPN | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |
> | vpnSites | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Iniziare con alfanumerico. Fine alfanumerico o sottolineatura. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | global | 6-50 | Alfanumerici e trattini<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / AuthorizationRules | namespace | 1-256 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Avviare alfanumerico. |
> | spazi dei nomi / notificationHubs | namespace | 1-260 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Avviare alfanumerico. |
> | spazi dei nomi / notificationHubs / AuthorizationRules | hub di notifica | 1-256 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Avviare alfanumerico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | gruppo di risorse | 4-63 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |
> | aree di lavoro | gruppo di risorse | 4-63 | Alfanumerici e trattini.<br><br>Iniziare e terminare con alfanumerici. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | dashboards | gruppo di risorse | 3-160 | Alfanumerici e trattini.<br><br>Per utilizzare caratteri con restrizioni, aggiungere un tag denominato **titolo nascosto** con il nome del dashboard che si desidera utilizzare. Il portale visualizza tale nome quando viene visualizzato il dashboard. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alfanumerici e trattini.<br><br>Non è possibile iniziare con il trattino. Impossibile utilizzare trattini consecutivi. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | Lettere o numeri minuscoli<br><br>Iniziare con la lettera minuscola. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | insiemi di credenziali | gruppo di risorse | 2-50 | Alfanumerici e trattini.<br><br>Inizia con la lettera. |
> | insiemi di credenziali / backupPolicies | insieme di credenziali | 3-150 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Non può terminare con il trattino. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | global | 6-50 | Alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Terminare con una lettera o un numero. |
> | spazi dei nomi / AuthorizationRules | namespace | 1-50 |  Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / HybridConnections | namespace | 1-260 | Alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / HybridConnections/authorizationRules | connessione ibrida | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / WcfRelays | namespace | 1-260 | Alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / WcfRelays / authorizationRules | Inoltro wcfWcf relay | 1-50 | Alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alfanumerici. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | deployments | gruppo di risorse | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura, parentesi, trattini e punti. |
> | gruppi di risorse | sottoscrizione | 1-90 | Caratteri alfanumerici, caratteri di sottolineatura, parentesi, trattini, punti e caratteri Unicode corrispondenti alla [documentazione dell'espressione regolare.](/rest/api/resources/resourcegroups/createorupdate)<br><br>Non posso finire con il punto. |
> | nomi di tag | resource | 1-512 | Non è possibile utilizzare:<br>`<>%&\?/` |
> | tagNames / tagValues | nome tag | 1-256 | Tutti i caratteri. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | global | 6-50 | Alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Terminare con una lettera o un numero.<br><br>Per ulteriori informazioni, vedere [Creare lo spazio dei nomi](/rest/api/servicebus/create-namespace). |
> | spazi dei nomi / AuthorizationRules | namespace | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alofnumerici. |
> | spazi dei nomi / disasterRecoveryConfigs | global | 6-50 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con alfanumerico. |
> | spazi dei nomi / migrationConfigurations | namespace |  | Dovrebbe sempre essere **$default**. |
> | spazi dei nomi / code | namespace | 1-260 | Alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / code / autorizzazioneRegole | coda | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alofnumerici. |
> | spazi dei nomi / argomenti | namespace | 1-260 | Alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Iniziare e terminare con alfanumerici. |
> | spazi dei nomi / argomenti / authorizationRules | argomento | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alofnumerici. |
> | spazi dei nomi / argomenti / sottoscrizioni | argomento | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alofnumerici. |
> | spazi dei nomi / argomenti / sottoscrizioni / regole | sottoscrizione | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Iniziare e terminare con alofnumerici. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | Lettere minuscole, numeri e trattini.<br><br>Iniziare con la lettera minuscola. Terminare con una lettera o un numero minuscolo. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Signalr | global | 3-63 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con la lettera o il numero.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | servers | global | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | server / amministratori | server |  | Deve essere `ActiveDirectory`. |
> | server / database | server | 1-128 | Non è possibile utilizzare:<br>`<>*%&:\/?`<br><br>Non può finire con punto o spazio. |
> | server / database / syncGroups | database | 1-150 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | server / elasticPools | server | 1-128 | Non è possibile utilizzare:<br>`<>*%&:\/?`<br><br>Non può finire con punto o spazio. |
> | server / failoverGruppi | global | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | server / firewallRegole | server | 1-128 | Non è possibile utilizzare:<br>`<>*%&:;\/?`<br><br>Non posso finire con il punto. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Lettere e numeri minuscoli. |
> | storageAccounts / blobServices | archiviazione di Azure |  | Deve essere `default`. |
> | storageAccounts / blobServices / contenitori | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Iniziare con una lettera o un numero minuscolo. Impossibile utilizzare trattini consecutivi. |
> | storageAccounts / fileServices | archiviazione di Azure |  | Deve essere `default`. |
> | storageAccounts / fileServices / condivisioni | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. Impossibile utilizzare trattini consecutivi. |
> | storageAccounts / managementPolicies | archiviazione di Azure |  | Deve essere `default`. |
> | blob | contenitore | 1-1024 | Qualsiasi carattere URL, distinzione tra maiuscole e minuscole |
> | coda | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. Impossibile utilizzare trattini consecutivi. |
> | tabella | archiviazione di Azure | 3-63 | Alfanumerici.<br><br>Inizia con la lettera. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | storageSyncServices | gruppo di risorse | 1-260 | Alfanumerici, spazi, punti, trattini e caratteri di sottolineatura.<br><br>Non può finire con punto o spazio. |
> | storageSyncServices / syncGroups | servizio di sincronizzazione archiviazione | 1-260 | Alfanumerici, spazi, punti, trattini e caratteri di sottolineatura.<br><br>Non può finire con punto o spazio. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managers | gruppo di risorse | 2-50 | Alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con alfanumerico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | streamingjobs | gruppo di risorse | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs / funzioni | lavoro in streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs / ingressi | lavoro in streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs / uscite | lavoro in streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs / trasformazioni | lavoro in streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | environments | gruppo di risorse | 1-90 | Non è possibile utilizzare:<br>`'<>%&:\?/#` |
> | ambienti / accessPolicies | environment | 1-90 | Non è possibile utilizzare:<br> `'<>%&:\?/#` |
> | ambienti / eventSources | environment | 1-90 | Non è possibile utilizzare:<br>`'<>%&:\?/#` |
> | ambienti /referenceDataSets | environment | 3-63 | Alfanumerici |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | serverfarms | gruppo di risorse | 1-40 | Alfanumerici e trattini. |
> | siti | global | 2-60 | Contiene caratteri alfanumerici e trattini.<br><br>Non è possibile iniziare o terminare con il trattino. |
> | siti / slot | site | 2-59 | Alfanumerici e trattini. |

## <a name="next-steps"></a>Passaggi successivi

Per suggerimenti su come assegnare un nome alle risorse, vedere [Pronto: Convenzioni di denominazione e assegnazione dei tag consigliate](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
