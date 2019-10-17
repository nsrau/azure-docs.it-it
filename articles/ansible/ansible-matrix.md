---
title: Modulo e matrice di versioni di Ansible per Azure | Microsoft Docs
description: Modulo Ansible e matrice della versione per Azure
keywords: ansible, ruoli, matrice, versione, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 275dca40ab20c222da2b9115f9a5dc141228c766
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385459"
---
# <a name="ansible-module-and-version-matrix"></a>Modulo Ansible e matrice della versione

Ansible include una suite di moduli da usare per il provisioning e la configurazione delle risorse di Azure. Queste risorse includono macchine virtuali, set di scalabilità, servizi di rete e servizi contenitore. Questo articolo elenca i vari moduli Ansible per Azure e le versioni di Ansible in cui sono forniti.

## <a name="ansible-modules-for-azure"></a>Moduli Ansible per Azure

I moduli seguenti possono essere eseguiti direttamente su host remoti o tramite PlayBook.  

Questi moduli sono disponibili dalla versione ufficiale di Ansible e dai ruoli di Microsoft PlayBook seguenti.

> [!NOTE]
> Da Ansible 2,9 in poi, abbiamo rinominato tutti i * moduli _facts in * _info per conformarsi alla convenzione di denominazione Ansible. I moduli obsoleti e rinominati sono collegati, oltre a visualizzare un avviso di deprecazione, tutti i moduli funzionano come prima.

| Modulo Ansible per Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2,8 | Ansible 2,9 | Ruolo Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Calcolo**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_availabilityset_info              | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_deployment                         | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_functionapp                        | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_functionapp_info                  | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_image                              | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_image_info                        | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_resource                           | -            | -                           | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_resource_info                     | -            | -                           | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_resourcegroup                      | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_resourcegroup_info                | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_virtualmachine                     | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachineextension            | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachineimage_info          | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachinescaleset             | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachinescaleset_info       | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | SÌ          | SÌ          | SÌ          |
| **Rete**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_dnsrecordset                       | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_dnsrecordset_info                 | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_dnszone                            | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_dnszone_info                      | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_loadbalancer                       | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_loadbalancer_info                 | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_networkinterface                   | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_networkinterface_info             | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_publicipaddress                    | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_publicipaddress_info              | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_route                              | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_routetable                         | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_routetable_info                   | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_securitygroup                      | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_subnet                             | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualnetwork                     | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualnetwork_info               | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | SÌ          | SÌ          |
| **Archiviazione**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_manageddisk_info                  | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_storageaccount                     | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_storageaccount_info               | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_storageblob                        | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_webapp                             | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_webapp_info                       | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | SÌ          | SÌ          | SÌ          |
| **Contenitori**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | SÌ          | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_aks                                | -            | -                           | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_aks_info                          | -            | -                           | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_containerinstance                  | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_containerregistry                  | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_containerregistry_info            | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | SÌ          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | SÌ          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | SÌ          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | SÌ          |
| **Database**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqldatabase                      | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqlserver                        | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqldatabase                 | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqlserver                   | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_sqldatabase                        | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | SÌ          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | SÌ          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_sqlserver                          | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_sqlserver_info                    | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| **Analisi**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| **Integrazione**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| **Sicurezza**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | SÌ          | SÌ          | SÌ          |
| azure_rm_keyvaultkey                        | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_keyvaultsecret                     | -            | SÌ                         | SÌ          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| **Monitoraggio di Azure**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_autoscale_info                    | -            | -                         | -          | SÌ          | SÌ          | SÌ          | SÌ          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | SÌ          | SÌ          | SÌ          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | SÌ          | SÌ          |
| **Gestione e governance**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| **Internet delle cose**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | SÌ        | SÌ          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | SÌ        | SÌ          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introduzione al ruolo playbook per Azure

Il [ruolo PlayBook di azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) include tutti i moduli di Azure più recenti. Gli aggiornamenti e le correzioni di bug vengono apportati in modo più tempestivo rispetto alla versione ufficiale di Ansible. Se si usa Ansible per il provisioning delle risorse di Azure, si consiglia di installare il ruolo PlayBook `azure_preview_module`.

Il ruolo PlayBook `azure_preview_module` viene rilasciato ogni tre settimane.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui ruoli PlayBook, vedere [creazione di PlayBook riutilizzabili](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 