---
title: Modulo Ansible e matrice della versione per Azure
description: Modulo Ansible e matrice della versione per Azure
ms.service: ansible
keywords: ansible, ruoli, matrice, versione, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: f62cc2df9e4ce815c4427b80e271ddc672748e4f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="ansible-module-and-version-matrix"></a>Modulo Ansible e matrice della versione

## <a name="ansible-modules-for-azure"></a>Moduli Ansible per Azure
Ansible viene fornito di serie con un insieme di moduli che è possibile eseguire tramite playbook o direttamente in host remoti.
Questo articolo elenca i moduli Ansible per Azure che possono effettuare il provisioning di risorse cloud di Azure, ad esempio macchine virtuali, reti e servizi contenitore. È possibile ottenere i moduli dalla versione ufficiale di Ansible o dai seguenti ruoli dei playbook pubblicati da Microsoft.

| Modulo Ansible per Azure                   |  Ansible 2.4 |  Ruolo playbook [azure_module](#introduction-to-azuremodule) |  Ruolo playbook [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Calcolo**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Sì          | Sì                         | Sì                                 | 
| azure_rm_availabilityset_facts              | Sì          | Sì                         | Sì                                 | 
| azure_rm_deployment                         | Sì          | Sì                         | Sì                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Sì          | Sì                         | Sì                                 | 
| azure_rm_virtualmachineimage_facts          | Sì          | Sì                         | Sì                                 | 
| azure_rm_resourcegroup                      | Sì          | Sì                         | Sì                                 | 
| azure_rm_resourcegroup_facts                | Sì          | Sì                         | Sì                                 | 
| azure_rm_virtualmachine                     | Sì          | Sì                         | Sì                                 | 
| azure_rm_virtualmachine_extension           | Sì          | Sì                         | Sì                                 | 
| azure_rm_virtualmachine_scaleset            | Sì          | Sì                         | Sì                                 | 
| azure_rm_image                              |              | Sì                         | Sì                                 | 
| **Rete**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Sì          | Sì                         | Sì                                 | 
| azure_rm_virtualnetwork_facts               | Sì          | Sì                         | Sì                                 | 
| azure_rm_subnet                             | Sì          | Sì                         | Sì                                 | 
| azure_rm_networkinterface                   | Sì          | Sì                         | Sì                                 | 
| azure_rm_networkinterface_facts             | Sì          | Sì                         | Sì                                 | 
| azure_rm_publicipaddress                    | Sì          | Sì                         | Sì                                 | 
| azure_rm_publicipaddress_facts              | Sì          | Sì                         | Sì                                 | 
| azure_rm_dnsrecordset                       | Sì          | Sì                         | Sì                                 | 
| azure_rm_dnsrecordset_facts                 | Sì          | Sì                         | Sì                                 | 
| azure_rm_dnszone                            | Sì          | Sì                         | Sì                                 | 
| azure_rm_dnszone_facts                      | Sì          | Sì                         | Sì                                 | 
| azure_rm_loadbalancer                       | Sì          | Sì                         | Sì                                 | 
| azure_rm_loadbalancer_facts                 | Sì          | Sì                         | Sì                                 | 
| azure_rm_appgw                              | -            | -                           | Sì                                 | 
| azure_rm_appgwroute                         | -            | -                           | Sì                                 | 
| azure_rm_appgwroute                         | -            | -                           | Sì                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Sì                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Sì                                 |
| azure_rm_securitygroup                      | Sì          | Sì                         | Sì                                 | 
| azure_rm_appgwroutetable_facts              | Sì          | Sì                         | Sì                                 | 
| **Archiviazione**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Sì          | Sì                         | Sì                                 | 
| azure_rm_storageaccount_facts               | Sì          | Sì                         | Sì                                 | 
| azure_rm_storageblob                        | Sì          | Sì                         | Sì                                 | 
| azure_rm_managed_disk                       | Sì          | Sì                         | Sì                                 | 
| azure_rm_managed_disk_facts                 | Sì          | Sì                         | Sì                                 | 
| **Contenitori**                    |           |                          |                                  | 
| azure_rm_acs                                | Sì          | Sì                         | Sì                                 | 
| azure_rm_containerinstance                  | -            | Sì                         | Sì                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Sì                                 | 
| azure_rm_containerregistry                  | -            | Sì                         | Sì                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Sì                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Sì                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Sì                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Sì                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Sì                                 | 
| **Funzioni di Azure**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Sì          | Sì                         | Sì                                 | 
| azure_rm_functionapp_facts                  | Sì          | Sì                         | Sì                                 | 
| **Database**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Sì                         | Sì                                 | 
| azure_rm_sqlserver_facts                    | -            | -                           | Sì                                 | 
| azure_rm_sqldatabase                        | -            | Sì                         | Sì                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Sì                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Sì                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Sì                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Sì                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Sì                                 | 
| azure_rm_mysqlserver                        | -            | Sì                         | Sì                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Sì                                 | 
| azure_rm_mysqldatabase                      | -            | Sì                         | Sì                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Sì                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Sì                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Sì                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Sì                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Sì                                 | 
| azure_rm_postgresqlserver                   | -            | Sì                         | Sì                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Sì                                 | 
| azure_rm_postgresqldatabase                 | -            | Sì                         | Sì                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Sì                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Sì                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Sì                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Sì                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Sì                                 | 
| **Insieme di credenziali di chiave**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | -                           | Sì                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Sì                                 |
| azure_rm_keyvaultkey                        | -            | -                           | Sì                                 |
| azure_rm_keyvaultsecret                     | -            | -                           | Sì                                 |

## <a name="introduction-to-azuremodule"></a>Introduzione ad azure_module
Il [ruolo playbook azure_module](https://galaxy.ansible.com/Azure/azure_modules/) include le ultime modifiche e correzioni di bug per i moduli di Azure ottenute dal [ramo sviluppo del repository Ansible](https://github.com/ansible/ansible/tree/devel). Se non è possibile attendere la versione successiva di Ansible, è opportuno installare il ruolo azure_module.

Il ruolo playbook azure_module viene rilasciato ogni tre settimane.

## <a name="introduction-to-azurepreviewmodule"></a>Introduzione ad azure_preview_module
Il [ruolo playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) è il ruolo più completo e include tutti i più recenti moduli di Azure. Gli aggiornamenti e le correzioni di bug vengono apportati in modo più tempestivo rispetto alla versione ufficiale di Ansible. Se si usa Ansible per il provisioning di risorse di Azure, si consiglia di installare il ruolo azure_preview_module.

Il ruolo playbook azure_preview_module viene rilasciato ogni tre settimane.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui ruoli di playbook, vedere [Creating Reusable Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Creazione di playbook riusabili). 
