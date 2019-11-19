---
title: Modulo e matrice di versioni di Ansible per Azure | Microsoft Docs
description: Modulo Ansible e matrice della versione per Azure
keywords: ansible, ruoli, matrice, versione, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155979"
---
# <a name="ansible-module-and-version-matrix"></a>Modulo Ansible e matrice della versione

Ansible include una suite di moduli da usare per il provisioning e la configurazione delle risorse di Azure. Queste risorse includono macchine virtuali, set di scalabilità, servizi di rete e servizi contenitore. Questo articolo elenca i vari moduli Ansible per Azure e le versioni di Ansible in cui sono forniti.

## <a name="ansible-modules-for-azure"></a>Moduli Ansible per Azure

I moduli seguenti possono essere eseguiti direttamente su host remoti o tramite PlayBook.  

Questi moduli sono disponibili dalla versione ufficiale di Ansible e dai ruoli di Microsoft PlayBook seguenti.

> [!NOTE]
> Da Ansible 2,9 in poi, abbiamo rinominato tutti i * _facts moduli in * _info per rispettare la convenzione di denominazione Ansible. I moduli obsoleti e rinominati sono collegati, oltre a visualizzare un avviso di deprecazione, tutti i moduli funzionano come prima.

| Modulo Ansible per Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2,8 | Ansible 2,9 | Ruolo Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Calcolo**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_availabilityset_info              | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_deployment                         | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_functionapp                        | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_functionapp_info                  | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_image                              | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_resource                           | -            | -                           | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_resource_info                     | -            | -                           | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_resourcegroup                      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_resourcegroup_info                | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_virtualmachine                     | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachineextension            | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualmachineimage_info          | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescaleset             | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescaleset_info       | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Sì          | Sì          | Sì          |
| **Rete**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| azure_rm_dnsrecordset                       | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_dnsrecordset_info                 | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_dnszone                            | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_dnszone_info                      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_loadbalancer                       | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_loadbalancer_info                 | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_networkinterface                   | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_networkinterface_info             | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_publicipaddress                    | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_publicipaddress_info              | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_route                              | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_routetable                         | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_routetable_info                   | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_securitygroup                      | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_subnet                             | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualnetwork                     | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualnetwork_info               | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Sì          | Sì          |
| **Archiviazione**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_manageddisk_info                  | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_storageaccount                     | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_storageaccount_info               | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_storageblob                        | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_webapp                             | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_webapp_info                       | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Sì          | Sì          | Sì          |
| **Contenitori**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Sì          | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_aks                                | -            | -                           | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_aks_info                          | -            | -                           | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_containerinstance                  | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_containerregistry                  | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Sì          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Sì          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Sì          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Sì          |
| **Database**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mysqldatabase                      | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_mysqlserver                        | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_postgresqldatabase                 | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_postgresqlserver                   | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_sqldatabase                        | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Sì          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Sì          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Sì          | Sì          | Sì          | Sì          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_sqlserver                          | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_sqlserver_info                    | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| **Analisi**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Sì          | Sì          |
| **Integrazione**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| **Sicurezza**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Sì          | Sì          | Sì          |
| azure_rm_keyvaultkey                        | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_keyvaultsecret                     | -            | Sì                         | Sì          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Sì          | Sì          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| **Monitoraggio di Azure**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Sì          | Sì          | Sì          | Sì          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Sì          | Sì          | Sì          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Sì          | Sì          |
| **Gestione e governance**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Sì        | Sì          |
| **Internet delle cose**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Sì        | Sì          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Sì        | Sì          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduzione al ruolo playbook per Azure

Il [ruolo playbook azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) include tutti i moduli di Azure più recenti. Gli aggiornamenti e le correzioni di bug vengono apportati in modo più tempestivo rispetto alla versione ufficiale di Ansible. Se si usa Ansible per il provisioning delle risorse di Azure, si consiglia di installare il ruolo PlayBook `azure_preview_module`.

Il ruolo PlayBook `azure_preview_module` viene rilasciato ogni tre settimane.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui ruoli PlayBook, vedere [creazione di PlayBook riutilizzabili](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 