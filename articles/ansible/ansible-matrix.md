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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230989"
---
# <a name="ansible-module-and-version-matrix"></a>Modulo Ansible e matrice della versione

Ansible include una suite di moduli da usare per il provisioning e la configurazione delle risorse di Azure. Queste risorse includono macchine virtuali, set di scalabilità, servizi di rete e servizi contenitore. Questo articolo elenca i vari moduli Ansible per Azure e le versioni di Ansible in cui sono inclusi.

## <a name="ansible-modules-for-azure"></a>Moduli Ansible per Azure

I moduli seguenti possono essere eseguiti tramite Playbook o direttamente su host remoti.

Questi moduli sono disponibili dalla versione ufficiale di Ansible e dei seguenti ruoli dei playbook Microsoft.

| Modulo Ansible per Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | 2\.8 Ansible | Ruolo Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Calcolo**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_availabilityset_facts              | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_deployment                         | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_functionapp                        | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_functionapp_facts                  | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_image                              | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_resource                           | -            | -                           | Yes          | Sì          | Sì          | Yes          |
| azure_rm_resource_facts                     | -            | -                           | Yes          | Sì          | Sì          | Yes          |
| azure_rm_resourcegroup                      | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_resourcegroup_facts                | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualmachine                     | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_virtualmachineextension           | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachineimage_facts          | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualmachinescaleset            | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualmachinescaleset_facts      | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Yes          | Yes          |
| **Rete**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_dnsrecordset                       | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_dnsrecordset_facts                 | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_dnszone                            | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_dnszone_facts                      | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_loadbalancer                       | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_loadbalancer_facts                 | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_networkinterface                   | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_networkinterface_facts             | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_publicipaddress                    | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_publicipaddress_facts              | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_route                              | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_securitygroup                      | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_subnet                             | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_virtualnetwork                     | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualnetwork_facts               | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Yes          | Yes          |
| **Archiviazione**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_manageddisk_facts                  | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_storageaccount                     | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_storageaccount_facts               | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_storageblob                        | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_webapp                             | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Yes          | Yes          |
| **Contenitori**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Yes          | Sì                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_aks                                | -            | -                           | Yes          | Sì          | Sì          | Yes          |
| azure_rm_aks_facts                          | -            | -                           | Yes          | Sì          | Sì          | Yes          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerinstance                  | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistry                  | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| **Database**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqldatabase                      | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlserver                        | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqldatabase                 | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlserver                   | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqldatabase                        | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Sì          | Yes          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlserver                          | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_sqlserver_facts                    | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| **Analisi**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Yes          | Yes          |
| **Integrazione**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Yes          | Yes          |
| **Sicurezza**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Yes          | Yes          |
| azure_rm_keyvaultkey                        | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Sì          | Sì          | Sì          | Yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| **Monitoraggio di Azure**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Yes          | Sì          | Yes          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Yes          | Yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduzione al ruolo playbook per Azure

Il [ruolo playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) include tutti i moduli Azure più recenti. Gli aggiornamenti e le correzioni di bug vengono apportati in modo più tempestivo rispetto alla versione ufficiale di Ansible. Se si usa Ansible per il provisioning di risorse Azure, si consiglia di installare il `azure_preview_module` ruolo playbook.

Il `azure_preview_module` ruolo playbook viene rilasciato ogni tre settimane.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui ruoli di playbook, vedere [creazione di Playbook riutilizzabili](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
