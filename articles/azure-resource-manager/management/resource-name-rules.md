---
title: Restrizioni di denominazione per le risorse
description: Illustra le regole e le restrizioni di denominazione per le risorse di Azure.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 553d0e33c4b9bc1aa238cf582f37be1e148eea5e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758895"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regole di denominazione e restrizioni per le risorse di Azure

Questo articolo riepiloga le regole e le restrizioni di denominazione per le risorse di Azure. Per consigli sull'assegnazione di nomi alle risorse, vedere [Convenzioni consigliate di denominazione e assegnazione di tag](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

I nomi delle risorse fanno distinzione tra maiuscole e minuscole solo se è indicato specificamente nella colonna dei caratteri validi.

Nelle tabelle seguenti il termine alfanumerico fa riferimento a:

* **a** - **z** (lettere minuscole)
* **A** - **Z** (lettere maiuscole)
* **0** - **9** (numeri)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | gruppo di risorse | 3-63 | Lettere minuscole e numeri.<br><br>Deve iniziare con una lettera minuscola. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | service | globale | 1-50 | Caratteri alfanumerici.<br><br>Deve iniziare con una lettera. |
> | service/apis | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/issues | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/issues/attachments | problema | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/issues/comments | problema | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/operations | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/operations/tags | operazione | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/releases | api | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico o di sottolineatura. |
> | service/apis/schemas | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/tagDescriptions | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/apis/tags | api | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/api-version-sets | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/authorizationServers | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/backends | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/certificates | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/diagnostics | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/groups | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/groups/users | group | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/identityProviders | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/loggers | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/notifications | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/notifications/recipientEmails | notifica | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/openidConnectProviders | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/policies | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/products | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/products/apis | product | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/products/groups | product | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/products/tags | product | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/properties | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/subscriptions | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/tags | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/templates | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |
> | service/users | service | 1-256 | Non è possibile usare:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | configurationStores | gruppo di risorse | 5-50 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | locks | ambito di assegnazione | 1-90 | Caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi.<br><br>Non può terminare con un punto. |
> | policyassignments | ambito di assegnazione | 1-128 nome visualizzato<br><br>1-260 nome risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della risorsa non può includere `%` e non può terminare con un punto o uno spazio. |
> | policydefinitions | ambito di definizione | 1-128 nome visualizzato<br><br>1-260 nome risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della risorsa non può includere `%` e non può terminare con un punto o uno spazio. |
> | policySetDefinitions | ambito di definizione | 1-128 nome visualizzato<br><br>1-260 nome risorsa | Il nome visualizzato può contenere qualsiasi carattere.<br><br>Il nome della risorsa non può includere `%` e non può terminare con un punto o uno spazio.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | automationAccounts | gruppo di risorse | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera e terminare con un carattere alfanumerico. |
> | automationAccounts/certificates | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio.  |
> | automationAccounts/connections | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/credentials | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/runbooks | account di automazione | 1-63 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Deve iniziare con una lettera.  |
> | automationAccounts/schedules | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/variables | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |
> | automationAccounts/watchers | account di automazione | 1-63 |  Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Deve iniziare con una lettera. |
> | automationAccounts/webhooks | account di automazione | 1-128 | Non è possibile usare:<br> `<>*%&:\?.+/` <br><br>Non può terminare con uno spazio. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Lettere minuscole e numeri. |
> | batchAccounts/applications | account Batch | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | batchAccounts/certificates | account Batch | 5-45 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | batchAccounts/pools | account Batch | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | blockchainMembers | globale | 2-20 | Lettere minuscole e numeri.<br><br>Deve iniziare con una lettera minuscola. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | botServices | globale | 2-64 |  Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |
> | botServices/channels | servizio bot | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |
> | botServices/Connections | servizio bot | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |
> | enterpriseChannels | gruppo di risorse | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Redis | globale | 1-63 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. I trattini consecutivi non sono consentiti. |
> | Redis/firewallRules | Redis | 1-256 | Caratteri alfanumerici |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Profili | gruppo di risorse | 1-260 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | profiles/endpoints | globale | 1-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | certificateOrders | gruppo di risorse | 3-30 | Caratteri alfanumerici. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | gruppo di risorse | 2-64 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | availabilitySets | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o di sottolineatura. |
> | diskEncryptionSets | gruppo di risorse | 1-80 | Caratteri alfanumerici e caratteri di sottolineatura. |
> | disks | gruppo di risorse | 1-80 | Caratteri alfanumerici e caratteri di sottolineatura. |
> | galleries | gruppo di risorse | 1-80 | Caratteri alfanumerici e punti.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | galleries/applications | gallery | 1-80 | Caratteri alfanumerici, trattini e punti.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | galleries/applications/versions | application | Intero a 32 bit | Numeri e punti. |
> | galleries/images | gallery | 1-80 | Caratteri alfanumerici, trattini e punti.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | galleries/images/versions | image | Intero a 32 bit | Numeri e punti. |
> | images | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o di sottolineatura. |
> | snapshots | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o di sottolineatura. |
> | virtualMachines | gruppo di risorse | 1-15 (Windows)<br>1-64 (Linux)<br><br>Vedere la nota che segue. | Non è possibile usare:<br> `\/""[]:|<>+=;,?*@&`<br><br>Non può iniziare con un carattere di sottolineatura. Non può terminare con un punto o un trattino. |
> | virtualMachineScaleSets | gruppo di risorse | 1-15 (Windows)<br>1-64 (Linux)<br><br>Vedere la nota che segue. | Non è possibile usare:<br> `\/""[]:|<>+=;,?*@&`<br><br>Non può iniziare con un carattere di sottolineatura. Non può terminare con un punto o un trattino. |

> [!NOTE]
> Le macchine virtuali di Azure hanno due nomi distinti: il nome della risorsa e il nome host. Quando si crea una macchina virtuale nel portale, viene usato lo stesso valore per entrambi i nomi. Le restrizioni nella tabella precedente sono relative al nome host. Il nome della risorsa effettivo può avere al massimo 64 caratteri.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | containerGroups | gruppo di risorse | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non può iniziare o terminare con un trattino. I trattini consecutivi non sono consentiti. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | registries | globale | 5-50 | Caratteri alfanumerici. |
> | registries/buildTasks | Registro di sistema | 5-50 | Caratteri alfanumerici. |
> | registries/buildTasks/steps | attività di compilazione | 5-50 | Caratteri alfanumerici. |
> | registries/replications | Registro di sistema | 5-50 | Caratteri alfanumerici. |
> | registries/scopeMaps | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registries/tasks | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registries/tokens | Registro di sistema | 5-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | registries/webhooks | Registro di sistema | 5-50 | Caratteri alfanumerici. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managedClusters | gruppo di risorse | 1-63 | Caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | openShiftManagedClusters | gruppo di risorse | 1-30 | Caratteri alfanumerici. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | hubs | gruppo di risorse | 1-64 | Caratteri alfanumerici.<br><br>Deve iniziare con una lettera.  |
> | hubs/authorizationPolicies | hub | 1-50 | Caratteri alfanumerici, caratteri di sottolineatura e punti.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | hubs/connectors | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/connectors/mappings | connettore | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/interactions | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/kpi | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/links | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/predictions | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/profiles | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/relationshipLinks | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/relationships | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/roleAssignments | hub | 1-128 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |
> | hubs/views | hub | 1-512 | Caratteri alfanumerici e caratteri di sottolineatura.<br><br>Deve iniziare con una lettera. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | associations | gruppo di risorse | 1-180 | Non è possibile usare:<br>`%&\\?/`<br><br>Non può terminare con un punto o uno spazio. |
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
> | factories | globale | 3-63 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | factories/dataflows | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Deve iniziare con un carattere alfanumerico. |
> | factories/datasets | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Deve iniziare con un carattere alfanumerico. |
> | factories/integrationRuntimes | factory | 3-63 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | factories/linkedservices | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Deve iniziare con un carattere alfanumerico. |
> | factories/pipelines | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Deve iniziare con un carattere alfanumerico. |
> | factories/triggers | factory | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Deve iniziare con un carattere alfanumerico. |
> | factories/triggers/rerunTriggers | trigger | 1-260 | Non è possibile usare:<br>`<>*#.%&:\\+?/`<br><br>Deve iniziare con un carattere alfanumerico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | globale | 3-24 | Lettere minuscole e numeri. |
> | accounts/computePolicies | account | 3-60 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | accounts/dataLakeStoreAccounts | account | 3-24 | Lettere minuscole e numeri. |
> | accounts/firewallRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | accounts/storageAccounts | account | 3-60 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | globale | 3-24 | Lettere minuscole e numeri. |
> | accounts/firewallRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | accounts/virtualNetworkRules | account | 3-50 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | services | gruppo di risorse | 2-62 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura.<br><br>Deve iniziare con un carattere alfanumerico. |
> | services/projects | service | 2-57 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura.<br><br>Deve iniziare con un carattere alfanumerico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | globale | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Non può iniziare o terminare con un trattino. |
> | servers/databases | servers | 1-63 | Caratteri alfanumerici e trattini. |
> | servers/firewallRules | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | servers/virtualNetworkRules | servers | 1-128 | Caratteri alfanumerici e trattini. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | globale | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Non può iniziare o terminare con un trattino. |
> | servers/databases | servers | 1-63 | Caratteri alfanumerici e trattini. |
> | servers/firewallRules | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | servers/virtualNetworkRules | servers | 1-128 | Caratteri alfanumerici e trattini. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | servers | globale | 3-63 | Lettere minuscole, trattini e numeri.<br><br>Non può iniziare o terminare con un trattino. |
> | servers/databases | servers | 1-63 | Caratteri alfanumerici e trattini. |
> | servers/firewallRules | servers | 1-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | servers/virtualNetworkRules | servers | 1-128 | Caratteri alfanumerici e trattini. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | Hub IoT | globale | 3-50 | Caratteri alfanumerici e trattini.<br><br>Non può terminare con un trattino. |
> | IotHubs/certificates | hub IoT | 1-64 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | provisioningServices | gruppo di risorse | 3-64 | Caratteri alfanumerici e trattini.<br><br>Deve terminare con un carattere alfanumerico. |
> | provisioningServices/certificates | provisioningServices | 1-64 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | labs | gruppo di risorse | 1-50 | Caratteri alfanumerici, caratteri di sottolineatura e trattini. |
> | labs/customimages | lab | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, trattini e parentesi. |
> | labs/formulas | lab | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, trattini e parentesi. |
> | labs/virtualmachines | lab | 1-15 (Windows)<br>1-64 (Linux) | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. Non può contenere solo numeri. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | databaseAccounts | globale | 3-44 | Lettere minuscole, numeri e trattini.<br><br>Deve iniziare con una lettera minuscola o un numero. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | domains | gruppo di risorse | 3-50 | Caratteri alfanumerici e trattini. |
> | domains/topics | dominio | 3-50 | Caratteri alfanumerici e trattini. |
> | eventSubscriptions | gruppo di risorse | 3-64 | Caratteri alfanumerici e trattini. |
> | topics | gruppo di risorse | 3-50 | Caratteri alfanumerici e trattini. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | gruppo di risorse | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con una lettera o un numero. |
> | spazi dei nomi | globale | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con una lettera o un numero. |
> | namespaces/AuthorizationRules | spazio dei nomi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con una lettera o un numero. |
> | namespaces/disasterRecoveryConfigs | spazio dei nomi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con una lettera o un numero. |
> | namespaces/eventhubs | spazio dei nomi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con una lettera o un numero. |
> | namespaces/eventhubs/authorizationRules | hub eventi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con una lettera o un numero. |
> | namespaces/eventhubs/consumergroups | hub eventi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con una lettera o un numero. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | globale | 3-59 | Caratteri alfanumerici e trattini<br><br>Deve iniziare e terminare con una lettera o un numero. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | jobs | gruppo di risorse | 2-64 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | components | gruppo di risorse | 1-260 | Non è possibile usare:<br>`%&\?/` <br><br>Non può terminare con uno spazio o un punto.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | IoTApps | globale | 2-63 | Lettere minuscole, numeri e trattini.<br><br>Deve iniziare con una lettera minuscola o un numero. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | insiemi di credenziali | globale | 3-24 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con una lettera o una cifra. Non può contenere trattini consecutivi. |
> | vaults/secrets | Insiemi di credenziali | 1-127 | Caratteri alfanumerici e trattini. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | globale | 4-22 | Lettere minuscole e numeri.<br><br>Deve iniziare con una lettera. |
> | /clusters/databases | cluster | 1-260 | Caratteri alfanumerici, trattini, spazi e punti. |
> | /clusters/databases/dataConnections | database | 1-40 | Caratteri alfanumerici, trattini, spazi e punti. |
> | /clusters/databases/eventhubconnections | database | 1-40 | Caratteri alfanumerici, trattini, spazi e punti. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | integrationAccounts | gruppo di risorse | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/assemblies | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/batchConfigurations | Connettori | 1-20 | Caratteri alfanumerici. |
> | integrationAccounts/certificates | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/maps | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/partners | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/rosettanetprocessconfigurations | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/schemas | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationAccounts/sessions | Connettori | 1-80 | Caratteri alfanumerici, trattini, caratteri di sottolineatura, punti e parentesi. |
> | integrationServiceEnvironments | gruppo di risorse | 1-80 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
> | integrationServiceEnvironments/managedApis | ambiente del servizio di integrazione | 1-80 | Caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. |
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
> | workspaces/computes | area di lavoro | 2-16 | Caratteri alfanumerici e trattini. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | userAssignedIdentities | gruppo di risorse | 3-128 | Caratteri alfanumerici, trattini e caratteri di sottolineatura<br><br>Deve iniziare con una lettera o un numero. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | account | gruppo di risorse | 1-98 (per il nome del gruppo di risorse e il nome dell'account) | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | mediaservices | gruppo di risorse | 3-24 | Lettere minuscole e numeri. |
> | mediaservices/liveEvents | servizio multimediale | 1-32 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |
> | mediaservices/liveEvents/liveOutputs | evento live | 1-256 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |
> | mediaservices/streamingEndpoints | servizio multimediale | 1-24 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con un carattere alfanumerico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | applicationGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | applicationSecurityGroups | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | azureFirewalls | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o di sottolineatura. |
> | bastionHosts | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | connections | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | dnsZones | gruppo di risorse | 1-63 caratteri<br><br>da 2 a 34 etichette<br><br>Ogni etichetta è un set di caratteri separati da un punto. Ad esempio, **contoso.com** ha due etichette. | Ogni etichetta può contenere caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Ogni etichetta è separata da un punto. |
> | expressRouteCircuits | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | firewallPolicies | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | firewallPolicies/ruleGroups | criterio firewall | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | frontDoors | globale | 5-64 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | loadBalancers | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | loadBalancers/inboundNatRules | bilanciamento del carico | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | localNetworkGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | networkInterfaces | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | networkSecurityGroups | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | networkSecurityGroups/securityRules | gruppo di sicurezza di rete | 1-80 |  Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | networkWatchers | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | privateDnsZones | gruppo di risorse | 1-63 caratteri<br><br>da 2 a 34 etichette<br><br>Ogni etichetta è un set di caratteri separati da un punto. Ad esempio, **contoso.com** ha due etichette. | Ogni etichetta può contenere caratteri alfanumerici, caratteri di sottolineatura e trattini.<br><br>Ogni etichetta è separata da un punto. |
> | privateDnsZones/virtualNetworkLinks | zona DNS privato | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | publicIPAddresses | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | publicIPPrefixes | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | routeFilters | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | routeFilters/routeFilterRules | filtro della route | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | routeTables | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | routeTables/routes | Tabella di route | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | serviceEndpointPolicies | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | trafficmanagerprofiles | globale | 1-63 | Caratteri alfanumerici, trattini e punti.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | virtualNetworkGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | virtualNetworks | gruppo di risorse | 2-64 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | virtualnetworks/subnets | rete virtuale | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | virtualNetworks/virtualNetworkPeerings | rete virtuale | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | virtualWans | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | vpnGateways | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | vpnGateways/vpnConnections | gateway VPN | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |
> | vpnSites | gruppo di risorse | 1-80 | Caratteri alfanumerici, caratteri di sottolineatura, punti e trattini.<br><br>Deve iniziare con un carattere alfanumerico. Deve terminare con un carattere alfanumerico o con un carattere di sottolineatura. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | globale | 6-50 | Caratteri alfanumerici e trattini<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/AuthorizationRules | spazio dei nomi | 1-256 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare con un carattere alfanumerico. |
> | namespaces/notificationHubs | spazio dei nomi | 1-260 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare con un carattere alfanumerico. |
> | namespaces/notificationHubs/AuthorizationRules | hub di notifica | 1-256 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare con un carattere alfanumerico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | gruppo di risorse | 4-63 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | aree di lavoro | gruppo di risorse | 4-63 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | solutions | area di lavoro | N/D | Per le soluzioni create da Microsoft, il nome deve seguire il modello:<br>`SolutionType(WorkspaceName)`<br><br>Per le soluzioni create da terze parti, il nome deve seguire il modello:<br>`SolutionType[WorkspaceName]`<br><br>Ad esempio, un nome valido è:<br>`AntiMalware(contoso-IT)`<br><br>Il tipo di soluzione fa distinzione tra maiuscole e minuscole. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | dashboards | gruppo di risorse | 3-160 | Caratteri alfanumerici e trattini.<br><br>Per usare caratteri con restrizioni, aggiungere il tag **hidden-title** con il nome del dashboard che si vuole usare. Il portale mostra questo nome quando viene visualizzato il dashboard. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Caratteri alfanumerici e trattini.<br><br>Non può iniziare con un trattino. Non può usare trattini consecutivi. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | Lettere minuscole o numeri<br><br>Deve iniziare con una lettera minuscola. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | insiemi di credenziali | gruppo di risorse | 2-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. |
> | vaults/backupPolicies | insieme di credenziali | 3-150 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Non può terminare con un trattino. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | globale | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con una lettera o un numero. |
> | namespaces/AuthorizationRules | spazio dei nomi | 1-50 |  Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/HybridConnections | spazio dei nomi | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/HybridConnections/authorizationRules | connessione ibrida | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/WcfRelays | spazio dei nomi | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/WcfRelays/authorizationRules | inoltro Wcf | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | deployments | gruppo di risorse | 1-64 | Caratteri alfanumerici, caratteri di sottolineatura, parentesi, trattini e punti. |
> | resourcegroups | sottoscrizione | 1-90 | Caratteri alfanumerici, caratteri di sottolineatura, parentesi, trattini, punti e caratteri Unicode corrispondenti alla [documentazione regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Non può terminare con un punto. |
> | tagNames | resource | 1-512 | Non è possibile usare:<br>`<>%&\?/` |
> | tagNames/tagValues | nome tag | 1-256 | Tutti i caratteri. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | spazi dei nomi | globale | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con una lettera o un numero.<br><br>Per altre informazioni, vedere [Creare uno spazio dei nomi](/rest/api/servicebus/create-namespace). |
> | namespaces/AuthorizationRules | spazio dei nomi | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/disasterRecoveryConfigs | globale | 6-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con un carattere alfanumerico. |
> | namespaces/migrationConfigurations | spazio dei nomi |  | Deve essere sempre **$default**. |
> | namespaces/queues | spazio dei nomi | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/queues/authorizationRules | coda | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/topics | spazio dei nomi | 1-260 | Caratteri alfanumerici, punti, trattini, caratteri di sottolineatura e barre.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/topics/authorizationRules | argomento | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/topics/subscriptions | argomento | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |
> | namespaces/topics/subscriptions/rules | sottoscrizione | 1-50 | Caratteri alfanumerici, punti, trattini e caratteri di sottolineatura.<br><br>Deve iniziare e terminare con un carattere alfanumerico. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | Lettere minuscole, numeri e trattini.<br><br>Deve iniziare con una lettera minuscola. Deve terminare con una lettera minuscola o un numero. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | signalR | globale | 3-63 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con una lettera o un numero.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managedInstances | globale | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non può iniziare o terminare con un trattino. |
> | servers | globale | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non può iniziare o terminare con un trattino. |
> | servers/administrators | server |  | Deve essere `ActiveDirectory`. |
> | servers/databases | server | 1-128 | Non è possibile usare:<br>`<>*%&:\/?`<br><br>Non può terminare con un punto o uno spazio. |
> | servers/databases/syncGroups | database | 1-150 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | servers/elasticPools | server | 1-128 | Non è possibile usare:<br>`<>*%&:\/?`<br><br>Non può terminare con un punto o uno spazio. |
> | servers/failoverGroups | globale | 1-63 | Lettere minuscole, numeri e trattini.<br><br>Non può iniziare o terminare con un trattino. |
> | servers/firewallRules | server | 1-128 | Non è possibile usare:<br>`<>*%&:;\/?`<br><br>Non può terminare con un punto. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | storageAccounts | globale | 3-24 | Lettere minuscole e numeri. |
> | storageAccounts/blobServices | archiviazione di Azure |  | Deve essere `default`. |
> | storageAccounts/blobServices/containers | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Deve iniziare con una lettera minuscola o un numero. Non può usare trattini consecutivi. |
> | storageAccounts/fileServices | archiviazione di Azure |  | Deve essere `default`. |
> | storageAccounts/fileServices/shares | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Non può iniziare o terminare con un trattino. Non può usare trattini consecutivi. |
> | storageAccounts/managementPolicies | archiviazione di Azure |  | Deve essere `default`. |
> | blob | contenitore | 1-1024 | Qualsiasi carattere URL. Fa distinzione tra maiuscole e minuscole |
> | coda | archiviazione di Azure | 3-63 | Lettere minuscole, numeri e trattini.<br><br>Non può iniziare o terminare con un trattino. Non può usare trattini consecutivi. |
> | tabella | archiviazione di Azure | 3-63 | Caratteri alfanumerici.<br><br>Deve iniziare con una lettera. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | storageSyncServices | gruppo di risorse | 1-260 | Caratteri alfanumerici, spazi, punti, trattini e caratteri di sottolineatura.<br><br>Non può terminare con un punto o uno spazio. |
> | storageSyncServices/syncGroups | servizio di sincronizzazione archiviazione | 1-260 | Caratteri alfanumerici, spazi, punti, trattini e caratteri di sottolineatura.<br><br>Non può terminare con un punto o uno spazio. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | managers | gruppo di risorse | 2-50 | Caratteri alfanumerici e trattini.<br><br>Deve iniziare con una lettera. Deve terminare con un carattere alfanumerico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | streamingjobs | gruppo di risorse | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs/functions | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs/inputs | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs/outputs | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |
> | streamingjobs/transformations | processo di streaming | 3-63 | Caratteri alfanumerici, trattini e caratteri di sottolineatura. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | environments | gruppo di risorse | 1-90 | Non è possibile usare:<br>`'<>%&:\?/#` |
> | environments/accessPolicies | environment | 1-90 | Non è possibile usare:<br> `'<>%&:\?/#` |
> | environments/eventSources | environment | 1-90 | Non è possibile usare:<br>`'<>%&:\?/#` |
> | environments/referenceDataSets | environment | 3-63 | Caratteri alfanumerici |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entità | Scope | Length | Caratteri validi |
> | --- | --- | --- | --- |
> | serverfarms | gruppo di risorse | 1-40 | Caratteri alfanumerici e trattini. |
> | siti | globale | 2-60 | Contiene caratteri alfanumerici e trattini.<br><br>Non può iniziare o terminare con un trattino. |
> | sites/slots | site | 2-59 | Caratteri alfanumerici e trattini. |

> [!NOTE]
> Funzioni di Azure ha le stesse regole di denominazione e le stesse restrizioni di Microsoft.Web/sites.

## <a name="next-steps"></a>Passaggi successivi

Per consigli su come assegnare un nome alle risorse, vedere [: Convenzioni di denominazione e assegnazione di tag consigliate](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
