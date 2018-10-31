---
title: Modulo Ansible e matrice della versione per Azure
description: Modulo Ansible e matrice della versione per Azure
ms.service: ansible
keywords: ansible, ruoli, matrice, versione, azure, devops
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: a71f28413028efe3547df7c464ec852fe2161f63
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649942"
---
# <a name="ansible-module-and-version-matrix"></a>Modulo Ansible e matrice della versione

## <a name="ansible-modules-for-azure"></a>Moduli Ansible per Azure
Ansible viene fornito di serie con un insieme di moduli che è possibile eseguire tramite playbook o direttamente in host remoti.
Questo articolo elenca i moduli Ansible per Azure che possono effettuare il provisioning di risorse cloud di Azure, ad esempio macchine virtuali, reti e servizi contenitore. È possibile ottenere i moduli dalla versione ufficiale di Ansible o dai seguenti ruoli dei playbook pubblicati da Microsoft.

| Modulo Ansible per Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | [Ruolo Ansible](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Calcolo**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_availabilityset_facts              | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_deployment                         | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_resource                           | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_resource_facts                     | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachineimage_facts          | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_resourcegroup_facts                | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_virtualmachine_extension           | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine_scaleset            | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_image                              |              | Yes                         | Yes          | Yes          | Yes                                 | 
| **Rete**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualnetwork_facts               | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_subnet                             | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_networkinterface_facts             | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_publicipaddress_facts              | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnsrecordset_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnszone                            | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnszone_facts                      | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_loadbalancer_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes          | Yes          | Yes                                 |
| azure_rm_route                              | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Yes          | Yes                                 | 
| **Archiviazione**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_storageaccount_facts               | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_storageblob                        | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_managed_disk                       | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_managed_disk_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| **Contenitori**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_aks_facts                          | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_acs                                | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_containerinstance                  | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Yes                                 | 
| azure_rm_containerregistry                  | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Yes                                 | 
| **Funzioni di Azure**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| **Database**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_sqlserver_facts                    | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_sqldatabase                        | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlserver                        | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Yes                                 | 
| **Insieme di credenziali di chiave**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Yes                         | Yes          | Yes          | Yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Yes                               |
| azure_rm_keyvaultkey                        | -            | Yes                         | Yes          | Yes          | Yes                                 |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Yes          | Yes          | Yes                                 |
| **App Web**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Yes          | Yes                                 | 
| **Gestione traffico**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Yes          | Yes                                 | 
| **Scalabilità automatica**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Yes          | Yes                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Introduzione al ruolo playbook per Azure
Il [ruolo playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) è il ruolo più completo e include tutti i più recenti moduli di Azure. Gli aggiornamenti e le correzioni di bug vengono apportati in modo più tempestivo rispetto alla versione ufficiale di Ansible. Se si usa Ansible per il provisioning di risorse di Azure, si consiglia di installare il ruolo playbook azure_preview_module.

Il ruolo playbook azure_preview_module viene rilasciato ogni tre settimane.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui ruoli di playbook, vedere [Creating Reusable Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Creazione di playbook riusabili). 
