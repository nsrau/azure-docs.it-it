---
title: Matrice di modulo e una versione di Ansible per Azure | Microsoft Docs
description: Modulo Ansible e matrice della versione per Azure
keywords: ansible, ruoli, matrice, versione, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b3396b2f4639cc7298b77810dbaafadd308d6b24
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230989"
---
# <a name="ansible-module-and-version-matrix"></a>Modulo Ansible e matrice della versione

Ansible include un gruppo di moduli per usarli nel provisioning e configurazione delle risorse di Azure. Queste risorse includono macchine virtuali, set di scalabilità, servizi e i servizi contenitore di rete. Questo articolo elenca i vari moduli Ansible per Azure e le versioni di Ansible in cui sono inclusi.

## <a name="ansible-modules-for-azure"></a>Moduli Ansible per Azure

I moduli seguenti possono essere eseguiti tramite Playbook o direttamente su host remoti.

Questi moduli sono disponibili dalla versione ufficiale di Ansible e dei seguenti ruoli dei playbook Microsoft.

| Modulo Ansible per Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | 2.8 Ansible | Ruolo Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Calcolo**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_availabilityset_facts              | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_deployment                         | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_functionapp                        | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_functionapp_facts                  | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_image                              | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_resource                           | -            | -                           | Sì          | Sì          | Sì          | Sì          |
| azure_rm_resource_facts                     | -            | -                           | Sì          | Sì          | Sì          | Sì          |
| azure_rm_resourcegroup                      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_resourcegroup_facts                | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachine                     | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualmachineextension           | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_virtualmachineimage_facts          | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescaleset            | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescaleset_facts      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Sì          | Sì          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Sì          | Sì          |
| **Rete**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Sì          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Sì          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Sì          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Sì          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Sì          | Sì          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Sì          | Sì          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Sì          | Sì          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Sì          | Sì          |
| azure_rm_dnsrecordset                       | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_dnsrecordset_facts                 | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_dnszone                            | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_dnszone_facts                      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_loadbalancer                       | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_loadbalancer_facts                 | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_networkinterface                   | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_networkinterface_facts             | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_publicipaddress                    | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_publicipaddress_facts              | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_route                              | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_routetable                         | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_securitygroup                      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_subnet                             | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_virtualnetwork                     | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualnetwork_facts               | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Sì          | Sì          |
| **Archiviazione**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_manageddisk_facts                  | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_storageaccount                     | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_storageaccount_facts               | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_storageblob                        | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_webapp                             | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Sì          | Sì          |
| **Contenitori**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_aks                                | -            | -                           | Sì          | Sì          | Sì          | Sì          |
| azure_rm_aks_facts                          | -            | -                           | Sì          | Sì          | Sì          | Sì          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_containerinstance                  | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_containerregistry                  | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Sì          | Sì          |
| **Database**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mysqldatabase                      | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_mysqlserver                        | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_postgresqldatabase                 | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_postgresqlserver                   | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_sqldatabase                        | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Sì          | Sì          | Sì          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_sqlserver                          | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_sqlserver_facts                    | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| **Analisi**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Sì          | Sì          |
| **Integrazione**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Sì          | Sì          |
| **Sicurezza**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Sì          | Sì          |
| azure_rm_keyvaultkey                        | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_keyvaultsecret                     | -            | Sì                         | Sì          | Sì          | Sì          | Sì          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Sì          | Sì          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Sì          | Sì          |
| **Monitoraggio di Azure**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Sì          | Sì          | Sì          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Sì          | Sì          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Sì          | Sì          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduzione al ruolo playbook per Azure

Il [ruolo playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) include tutti i moduli Azure più recenti. Gli aggiornamenti e le correzioni di bug vengono apportati in modo più tempestivo rispetto alla versione ufficiale di Ansible. Se si usa Ansible per il provisioning di risorse Azure, si consiglia di installare il `azure_preview_module` ruolo playbook.

Il `azure_preview_module` ruolo playbook viene rilasciato ogni tre settimane.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui ruoli di playbook, vedere [creazione di Playbook riutilizzabili](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
