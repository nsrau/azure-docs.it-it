---
title: Restrizioni relative alla denominazione delle risorse
description: Mostra le regole e le restrizioni per la denominazione delle risorse di Azure.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: fab1ab2bb779b3826c852e49da7970030d34594d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086386"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regole di denominazione e restrizioni per le risorse di Azure

Questo articolo riepiloga le regole di denominazione e le restrizioni per le risorse di Azure. Per consigli su come assegnare un nome alle risorse, vedere [convenzioni di denominazione e assegnazione di tag consigliate](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

I nomi delle risorse non fanno distinzione tra maiuscole e minuscole, a meno che non sia indicato specificamente nella colonna caratteri validi.

Nelle tabelle seguenti il termine alfanumerico fa riferimento a:

* **da a a** **z** (lettere minuscole)
* **Da A A** **Z** (lettere maiuscole)
* da **0** a **9** (numeri)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | gruppo di risorse | 3-63 | Lettere minuscole e numeri.<br><br>Inizia con una lettera minuscola. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | service | global | 1-50 | Alfanumerici.<br><br>Inizia con la lettera. |
> | servizio/API | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/problemi | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/problemi/allegati | problema | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/problemi/Commenti | problema | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/operazioni | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/operazioni/Tag | operazione | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/versioni | api | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Inizia e termina con un carattere alfanumerico o di sottolineatura. |
> | servizio/API/schemi | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/tagDescriptions | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/API/Tag | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | Service/API-Version-sets | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/authorizationServers | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/backend | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/certificati | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/diagnostica | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/gruppi | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/gruppi/utenti | group | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/non | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/logger | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/notifiche | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/notifiche/recipientEmails | notifica | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/openidConnectProviders | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/criteri | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/prodotti | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/prodotti/API | product | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/prodotti/gruppi | product | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/prodotti/Tag | product | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/proprietà | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/sottoscrizioni | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/Tag | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/modelli | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | servizio/utenti | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | configurationStores | gruppo di risorse | 5-50 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | locks | ambito di assegnazione | 1-90 | Caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi.<br><br>Non può terminare con un periodo di tempo. |
> | policyassignments | ambito di assegnazione | 1-128 nome visualizzato<br><br>1-260 nome della risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della risorsa `%` non può includere e non può terminare con un punto o uno spazio. |
> | PolicyDefinitions | ambito di definizione | 1-128 nome visualizzato<br><br>1-260 nome della risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della risorsa `%` non può includere e non può terminare con un punto o uno spazio. |
> | policySetDefinitions | ambito di definizione | 1-128 nome visualizzato<br><br>1-260 nome della risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della risorsa `%` non può includere e non può terminare con un punto o uno spazio.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | automationAccounts | gruppo di risorse | 6-50 | Caratteri alfanumerici e trattini.<br><br>Iniziare con una lettera e terminare con un carattere alfanumerico. |
> | automationAccounts/certificati | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio.  |
> | automationAccounts/connessioni | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/credenziali | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/manuali operativi | account di automazione | 1-63 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Inizia con la lettera.  |
> | automationAccounts/pianificazioni | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/variabili | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/Watcher | account di automazione | 1-63 |  Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Inizia con la lettera. |
> | automationAccounts/webhook | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Lettere minuscole e numeri. |
> | batchAccounts/applicazioni | account batch | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | batchAccounts/certificati | account batch | 5-45 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | batchAccounts/pool | account batch | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Lettere minuscole e numeri.<br><br>Inizia con una lettera minuscola. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. |
> | botServices/canali | servizio bot | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. |
> | botServices/connessioni | servizio bot | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. |
> | enterpriseChannels | gruppo di risorse | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. Trattini consecutivi non consentiti. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumerici |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | profiles | gruppo di risorse | 1-260 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |
> | profili/endpoint | global | 1-50 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | certificateOrders | gruppo di risorse | 3-30 | Alfanumerici. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | gruppo di risorse | 2-64 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | availabilitySets | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Termina con un carattere alfanumerico o di sottolineatura. |
> | diskEncryptionSets | gruppo di risorse | 1-80 | Caratteri alfanumerici e caratteri di sottolineatura. |
> | disks | gruppo di risorse | 1-80 | Caratteri alfanumerici e caratteri di sottolineatura. |
> | galleries | gruppo di risorse | 1-80 | Caratteri alfanumerici e punti.<br><br>Inizio e fine con alfanumerico. |
> | raccolte/applicazioni | raccolta | 1-80 | Caratteri alfanumerici, trattini e punti.<br><br>Inizio e fine con alfanumerico. |
> | raccolte/applicazioni/versioni | application | Intero a 32 bit | Numeri e punti. |
> | raccolte/immagini | raccolta | 1-80 | Caratteri alfanumerici, trattini e punti.<br><br>Inizio e fine con alfanumerico. |
> | raccolte/immagini/versioni | image | Intero a 32 bit | Numeri e punti. |
> | images | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Termina con un carattere alfanumerico o di sottolineatura. |
> | snapshots | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Termina con un carattere alfanumerico o di sottolineatura. |
> | virtualMachines | gruppo di risorse | 1-15 (Windows)<br>1-64 (Linux)<br><br>Vedere la nota seguente. | Non è possibile usare:<br> `\/""[]:|<>+=;,?*@&`<br><br>Non può iniziare con il carattere di sottolineatura. Non può terminare con un punto o un segno meno. |
> | virtualMachineScaleSets | gruppo di risorse | 1-15 (Windows)<br>1-64 (Linux)<br><br>Vedere la nota seguente. | Non è possibile usare:<br> `\/""[]:|<>+=;,?*@&`<br><br>Non può iniziare con il carattere di sottolineatura. Non può terminare con un punto o un segno meno. |

> [!NOTE]
> Le macchine virtuali di Azure hanno due nomi distinti: il nome della risorsa e il nome host. Quando si crea una macchina virtuale nel portale, viene usato lo stesso valore per entrambi i nomi. Le restrizioni nella tabella precedente sono relative al nome host. Il nome della risorsa effettivo può avere al massimo 64 caratteri.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | containerGroups | gruppo di risorse | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Impossibile iniziare o terminare con il trattino. Non sono consentiti trattini consecutivi. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | registries | global | 5-50 | Alfanumerici. |
> | registri/buildTasks | Registro di sistema | 5-50 | Alfanumerici. |
> | registri/buildTasks/passaggi | attività di compilazione | 5-50 | Alfanumerici. |
> | registri/repliche | Registro di sistema | 5-50 | Alfanumerici. |
> | registri/scopeMaps | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registri/attività | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registri/token | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registri/webhook | Registro di sistema | 5-50 | Alfanumerici. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managedClusters | gruppo di risorse | 1-63 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Inizio e fine con alfanumerico. |
> | openShiftManagedClusters | gruppo di risorse | 1-30 | Alfanumerici. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | hubs | gruppo di risorse | 1-64 | Alfanumerici.<br><br>Inizia con la lettera.  |
> | Hub/authorizationPolicies | hub | 1-50 | Caratteri alfanumerici, caratteri di sottolineatura e punti.<br><br>Inizio e fine con alfanumerico. |
> | Hub/connettori | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/connettori/mapping | connettore | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/interazioni | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/KPI | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/collegamenti | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/stime | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/profili | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/relationshipLinks | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/relazioni | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/roleAssignment | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |
> | Hub/viste | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Inizia con la lettera. |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | associazioni | gruppo di risorse | 1-180 | Non è possibile usare:<br>`%&\\?/`<br><br>Non può terminare con un punto o uno spazio. |
> | resourceProviders | gruppo di risorse | 3-64 | Non è possibile usare:<br>`%&\\?/`<br><br>Non può terminare con un punto o uno spazio. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | jobs | gruppo di risorse | 3-24 | Caratteri alfanumerici, trattini, caratteri di sottolineatura e punti. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | aree di lavoro | gruppo di risorse | 3-30 | Caratteri alfanumerici, caratteri di sottolineatura e trattini |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | factories | global | 3-63 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |
> | Factory/flussi di data | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Inizia con alfanumerico. |
> | Factory/set di impostazioni | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Inizia con alfanumerico. |
> | Factory/integrationRuntimes | factory | 3-63 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |
> | Factory/linkedservices | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Inizia con alfanumerico. |
> | Factory/pipeline | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Inizia con alfanumerico. |
> | Factory/trigger | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Inizia con alfanumerico. |
> | Factory/trigger/rerunTriggers | trigger | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Inizia con alfanumerico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | global | 3-24 | Lettere minuscole e numeri. |
> | account/computePolicies | account | 3-60 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | account/dataLakeStoreAccounts | account | 3-24 | Lettere minuscole e numeri. |
> | account/firewallRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | account/storageAccounts | account | 3-60 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | global | 3-24 | Lettere minuscole e numeri. |
> | account/firewallRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | account/virtualNetworkRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | services | gruppo di risorse | 2-62 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura.<br><br>Inizia con alfanumerico. |
> | Servizi/progetti | service | 2-57 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura.<br><br>Inizia con alfanumerico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Impossibile iniziare o terminare con il trattino. |
> | server/database | servers | 1-63 | Caratteri alfanumerici e trattini. |
> | Server/firewallRules | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | Server/virtualNetworkRules | servers | 1-128 | Caratteri alfanumerici e trattini. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Impossibile iniziare o terminare con il trattino. |
> | server/database | servers | 1-63 | Caratteri alfanumerici e trattini. |
> | Server/firewallRules | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | Server/virtualNetworkRules | servers | 1-128 | Caratteri alfanumerici e trattini. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Impossibile iniziare o terminare con il trattino. |
> | server/database | servers | 1-63 | Caratteri alfanumerici e trattini. |
> | Server/firewallRules | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | Server/virtualNetworkRules | servers | 1-128 | Caratteri alfanumerici e trattini. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Hub IoT | global | 3-50 | Caratteri alfanumerici e trattini.<br><br>Non può terminare con un trattino. |
> | IotHubs/certificati | Hub Internet delle cose | 1-64 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | provisioningServices | gruppo di risorse | 3-64 | Caratteri alfanumerici e trattini.<br><br>Termina con alfanumerico. |
> | provisioningServices/certificati | provisioningServices | 1-64 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | labs | gruppo di risorse | 1-50 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | Lab/CustomImages | laboratorio | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, trattini e parentesi. |
> | Lab/formule | laboratorio | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, trattini e parentesi. |
> | Lab/VirtualMachines | laboratorio | 1-15 (Windows)<br>1-64 (Linux) | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. Non può essere costituito solo da numeri. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | Lettere minuscole, numeri e trattini.<br><br>Inizia con una lettera minuscola o un numero. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | domains | gruppo di risorse | 3-50 | Caratteri alfanumerici e trattini. |
> | domini/argomenti | dominio | 3-50 | Caratteri alfanumerici e trattini. |
> | eventSubscriptions | gruppo di risorse | 3-64 | Caratteri alfanumerici e trattini. |
> | topics | gruppo di risorse | 3-50 | Caratteri alfanumerici e trattini. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | gruppo di risorse | 6-50 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con una lettera o un numero. |
> | spazi dei nomi | global | 6-50 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con una lettera o un numero. |
> | spazi dei nomi/AuthorizationRules | namespace | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con lettera o numero. |
> | spazi dei nomi/disasterRecoveryConfigs | namespace | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con lettera o numero. |
> | spazi dei nomi/Eventhubs | namespace | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con lettera o numero. |
> | spazi dei nomi/Eventhubs/authorizationRules | Hub eventi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con lettera o numero. |
> | spazi dei nomi/Eventhubs/consumergroups | Hub eventi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con lettera o numero. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | global | 3-59 | Caratteri alfanumerici e trattini<br><br>Inizio e fine con lettera o numero. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | jobs | gruppo di risorse | 2-64 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | components | gruppo di risorse | 1-260 | Non è possibile usare:<br>`%&\?/` <br><br>Non può terminare con uno spazio o un punto.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Lettere minuscole, numeri e trattini.<br><br>Inizia con una lettera minuscola o un numero. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | insiemi di credenziali | global | 3-24 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con una lettera o una cifra. Non può contenere trattini consecutivi. |
> | insiemi di credenziali/segreti | Insiemi di credenziali | 1-127 | Caratteri alfanumerici e trattini. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | global | 4-22 | Lettere minuscole e numeri.<br><br>Inizia con la lettera. |
> | /Clusters/database | cluster | 1-260 | Caratteri alfanumerici, trattini, spazi e punti. |
> | /clusters di database/DataConnection | database | 1-40 | Caratteri alfanumerici, trattini, spazi e punti. |
> | /Clusters/databases/eventhubconnections | database | 1-40 | Caratteri alfanumerici, trattini, spazi e punti. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | integrationAccounts | gruppo di risorse | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/assembly | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts / batchConfigurations | Connettori | 1-20 | Alfanumerici. |
> | integrationAccounts/certificati | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/Maps | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/partner | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts / rosettanetprocessconfigurations | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/schemi | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/sessioni | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationServiceEnvironments | gruppo di risorse | 1-80 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | integrationServiceEnvironments / managedApis | ambiente del servizio di integrazione | 1-80 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | flussi di lavoro | gruppo di risorse | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | commitmentPlans | gruppo di risorse | 1-260 | Non è possibile usare:<br>`<>*%&:?+/\\`<br><br>Non può terminare con uno spazio. |
> | webServices | gruppo di risorse | 1-260 | Non è possibile usare:<br>`<>*%&:?+/\\`<br><br>Non può terminare con uno spazio. |
> | aree di lavoro | gruppo di risorse | 1-260 | Non è possibile usare:<br>`<>*%&:?+/\\`<br><br>Non può terminare con uno spazio. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | aree di lavoro | gruppo di risorse | 3-33 | Caratteri alfanumerici e trattini. |
> | aree di lavoro/calcoli | area di lavoro | 2-16 | Caratteri alfanumerici e trattini. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | userAssignedIdentities | gruppo di risorse | 3-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura<br><br>Iniziare con una lettera o un numero. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | gruppo di risorse | 1-98 (per il nome del gruppo di risorse e il nome dell'account) | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | mediaservices | gruppo di risorse | 3-24 | Lettere minuscole e numeri. |
> | MediaServices/liveEvents | Servizio multimediale | 1-32 | Caratteri alfanumerici e trattini.<br><br>Inizia con alfanumerico. |
> | MediaServices/liveEvents/liveOutputs | Evento Live | 1-256 | Caratteri alfanumerici e trattini.<br><br>Inizia con alfanumerico. |
> | MediaServices/le entità streamingendpoint | Servizio multimediale | 1-24 | Caratteri alfanumerici e trattini.<br><br>Inizia con alfanumerico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | applicationGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | applicationSecurityGroups | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | azureFirewalls | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Termina con un carattere alfanumerico o di sottolineatura. |
> | bastionHosts | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | connections | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | dnsZones | gruppo di risorse | 1-63 caratteri<br><br>da 2 a 34 etichette<br><br>Ogni etichetta è un set di caratteri separati da un punto. Ad esempio, **contoso.com** ha 2 etichette. | Ogni etichetta può contenere caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Ogni etichetta è separata da un punto. |
> | expressRouteCircuits | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | firewallPolicies | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | firewallPolicies/regole | criteri firewall | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | frontDoors | global | 5-64 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |
> | loadBalancers | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | loadBalancers/inboundNatRules | bilanciamento del carico | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | localNetworkGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | networkInterfaces | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | networkSecurityGroups | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | networkSecurityGroups/securityRules | gruppo di sicurezza di rete | 1-80 |  Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | networkWatchers | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | privateDnsZones | gruppo di risorse | 1-63 caratteri<br><br>da 2 a 34 etichette<br><br>Ogni etichetta è un set di caratteri separati da un punto. Ad esempio, **contoso.com** ha 2 etichette. | Ogni etichetta può contenere caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Ogni etichetta è separata da un punto. |
> | privateDnsZones / virtualNetworkLinks | zona DNS privata | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | publicIPAddresses | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | publicIPPrefixes | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | routeFilters | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | routeFilters / routeFilterRules | filtro della route | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | routeTables | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | routeTables/Route | Tabella di route | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | serviceEndpointPolicies | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | trafficmanagerprofiles | global | 1-63 | Caratteri alfanumerici, trattini e punti.<br><br>Inizio e fine con alfanumerico. |
> | virtualNetworkGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | virtualNetworks | gruppo di risorse | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | virtualnetworks/subnet | rete virtuale | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | virtualNetworks/virtualNetworkPeerings | rete virtuale | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | virtualWans | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | vpnGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | vpnGateways / vpnConnections | gateway VPN | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |
> | vpnSites | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Inizia con alfanumerico. Fine carattere alfanumerico o carattere di sottolineatura. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | global | 6-50 | Caratteri alfanumerici e trattini<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/AuthorizationRules | namespace | 1-256 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Avvia alfanumerico. |
> | spazi dei nomi/notificationHubs | namespace | 1-260 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Avvia alfanumerico. |
> | spazi dei nomi/notificationHubs/AuthorizationRules | Hub di notifica | 1-256 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Avvia alfanumerico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | gruppo di risorse | 4-63 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |
> | aree di lavoro | gruppo di risorse | 4-63 | Caratteri alfanumerici e trattini.<br><br>Inizio e fine con alfanumerico. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | solutions | area di lavoro | N/D | Per le soluzioni create da Microsoft, il nome deve essere nel modello:<br>`SolutionType(WorkspaceName)`<br><br>Per le soluzioni create da terze parti, il nome deve essere nel modello:<br>`SolutionType[WorkspaceName]`<br><br>Ad esempio, un nome valido è:<br>`AntiMalware(contoso-IT)`<br><br>Il tipo di soluzione fa distinzione tra maiuscole e minuscole. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | dashboards | gruppo di risorse | 3-160 | Caratteri alfanumerici e trattini.<br><br>Per usare i caratteri limitati, aggiungere un tag denominato **Hidden-title** con il nome del dashboard che si vuole usare. Il portale Visualizza questo nome quando viene visualizzato il dashboard. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Caratteri alfanumerici e trattini.<br><br>Impossibile iniziare con il trattino. Non è possibile usare trattini consecutivi. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | Lettere minuscole o numeri<br><br>Inizia con una lettera minuscola. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | insiemi di credenziali | gruppo di risorse | 2-50 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. |
> | insiemi di credenziali/backupPolicies | insieme di credenziali | 3-150 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Non può terminare con un trattino. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | global | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Terminare con una lettera o un numero. |
> | spazi dei nomi/AuthorizationRules | namespace | 1-50 |  Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/HybridConnections | namespace | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/HybridConnections/authorizationRules | connessione ibrida | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/WcfRelays | namespace | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/WcfRelays/authorizationRules | Inoltro WCF | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizio e fine con alfanumerico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | deployments | gruppo di risorse | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura, parentesi, trattini e punti. |
> | ResourceGroups | sottoscrizione | 1-90 | Caratteri alfanumerici, caratteri di sottolineatura, parentesi, trattini, punti e caratteri Unicode che corrispondono alla [documentazione Regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Non può terminare con un punto. |
> | tagNames | risorse | 1-512 | Non è possibile usare:<br>`<>%&\?/` |
> | tagNames/tagValues | nome tag | 1-256 | Tutti i caratteri. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | global | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Terminare con una lettera o un numero.<br><br>Per altre informazioni, vedere [create namespace](/rest/api/servicebus/create-namespace). |
> | spazi dei nomi/AuthorizationRules | namespace | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizia e termina con alphnumeric. |
> | spazi dei nomi/disasterRecoveryConfigs | global | 6-50 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Termina con alfanumerico. |
> | spazi dei nomi/migrationConfigurations | namespace |  | Deve sempre essere **$default**. |
> | spazi dei nomi/code | namespace | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/code/authorizationRules | coda | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizia e termina con alphnumeric. |
> | spazi dei nomi/argomenti | namespace | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Inizio e fine con alfanumerico. |
> | spazi dei nomi/argomenti/authorizationRules | argomento | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizia e termina con alphnumeric. |
> | spazi dei nomi/argomenti/sottoscrizioni | argomento | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizia e termina con alphnumeric. |
> | spazi dei nomi/argomenti/sottoscrizioni/regole | sottoscrizione | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Inizia e termina con alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | Lettere minuscole, numeri e trattini.<br><br>Inizia con una lettera minuscola. Terminare con una lettera o un numero minuscolo. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | signalR | global | 3-63 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Terminare con una lettera o un numero.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Impossibile iniziare o terminare con il trattino. |
> | servers | global | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Impossibile iniziare o terminare con il trattino. |
> | Server/amministratori | server |  | Deve essere `ActiveDirectory`. |
> | server/database | server | 1-128 | Non è possibile usare:<br>`<>*%&:\/?`<br><br>Non può terminare con un punto o uno spazio. |
> | Servers/databases/syncGroups | database | 1-150 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | Server/elasticPools | server | 1-128 | Non è possibile usare:<br>`<>*%&:\/?`<br><br>Non può terminare con un punto o uno spazio. |
> | Server/failoverGroups | global | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Impossibile iniziare o terminare con il trattino. |
> | Server/firewallRules | server | 1-128 | Non è possibile usare:<br>`<>*%&:;\/?`<br><br>Non può terminare con un punto. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Lettere minuscole e numeri. |
> | storageAccounts/blobServices | archiviazione di Azure |  | Deve essere `default`. |
> | storageAccounts/blobServices/contenitori | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Inizia con una lettera minuscola o un numero. Non è possibile usare trattini consecutivi. |
> | storageAccounts/FileService | archiviazione di Azure |  | Deve essere `default`. |
> | storageAccounts/FileService/condivisioni | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Impossibile iniziare o terminare con il trattino. Non è possibile usare trattini consecutivi. |
> | storageAccounts/managementPolicies | archiviazione di Azure |  | Deve essere `default`. |
> | blob | contenitore | 1-1024 | Qualsiasi carattere URL, con distinzione tra maiuscole e minuscole |
> | coda | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Impossibile iniziare o terminare con il trattino. Non è possibile usare trattini consecutivi. |
> | tabella | archiviazione di Azure | 3-63 | Alfanumerici.<br><br>Inizia con la lettera. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | storageSyncServices | gruppo di risorse | 1-260 | Caratteri alfanumerici, spazi, punti, trattini e caratteri di sottolineatura.<br><br>Non può terminare con un punto o uno spazio. |
> | storageSyncServices / syncGroups | servizio di sincronizzazione archiviazione | 1-260 | Caratteri alfanumerici, spazi, punti, trattini e caratteri di sottolineatura.<br><br>Non può terminare con un punto o uno spazio. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managers | gruppo di risorse | 2-50 | Caratteri alfanumerici e trattini.<br><br>Inizia con la lettera. Termina con alfanumerico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | streamingjobs | gruppo di risorse | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streaming/funzioni | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streaming/input | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | Streaming/output | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streaming/trasformazioni | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | environments | gruppo di risorse | 1-90 | Non è possibile usare:<br>`'<>%&:\?/#` |
> | ambienti/accessPolicies | environment | 1-90 | Non è possibile usare:<br> `'<>%&:\?/#` |
> | ambienti/eventSources | environment | 1-90 | Non è possibile usare:<br>`'<>%&:\?/#` |
> | ambienti/referenceDataSets | environment | 3-63 | Alfanumerici |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | serverfarms | gruppo di risorse | 1-40 | Caratteri alfanumerici e trattini. |
> | siti | global | 2-60 | Contiene caratteri alfanumerici e trattini.<br><br>Impossibile iniziare o terminare con il trattino. |
> | siti/slot | site | 2-59 | Caratteri alfanumerici e trattini. |

## <a name="next-steps"></a>Passaggi successivi

Per consigli su come assegnare un nome alle risorse, vedere [Ready: convenzioni di denominazione e assegnazione di tag consigliate](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
