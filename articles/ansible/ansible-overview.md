---
title: Uso di Ansible con Azure
description: Introduzione all'uso di Ansible per automatizzare il provisioning cloud, la gestione della configurazione e le distribuzioni di applicazioni.
ms.service: ansible
keywords: ansible, azure, devops, panoramica, provisioning cloud, gestione della configurazione, distribuzione di applicazioni, moduli ansible, playbook ansible
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="ansible-with-azure"></a>Ansible con Azure

[Ansible](http://www.ansible.com) è un prodotto open source che consente di automatizzare il provisioning cloud, la gestione della configurazione e le distribuzioni di applicazioni. Tramite Ansible è possibile eseguire il provisioning di macchine virtuali, contenitori e reti, nonché completare le infrastrutture cloud. Ansible consente inoltre di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso.

In questo articolo viene presentata una panoramica di base di alcuni dei vantaggi derivanti dall'uso di Ansible con Azure.

## <a name="ansible-playbooks"></a>Playbook Ansible

I [playbook Ansible](http://docs.ansible.com/ansible/latest/playbooks.html) rappresentano il linguaggio di configurazione, distribuzione e orchestrazione di Ansible. Possono descrivere i criteri che si desidera applicare ai sistemi remoti oppure una serie di passaggi in un processo IT generico. Quando si crea un playbook, si usa il linguaggio YAML, che definisce un modello per una configurazione o un processo.

## <a name="ansible-modules"></a>Moduli Ansible

Ansible include un gruppo di [moduli Ansible](http://docs.ansible.com/ansible/latest/modules_by_category.html) che è possibile eseguire direttamente su host remoti o tramite [playbook](http://docs.ansible.com/ansible/latest/playbooks.html). Gli utenti possono anche creare moduli personalizzati. I moduli possono essere usati per controllare le risorse di sistema, ad esempio servizi, pacchetti o file, oppure per eseguire i comandi di sistema.

Per l'interazione con i servizi di Azure, Ansible include un gruppo di [moduli cloud Ansible](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) che forniscono gli strumenti necessari per semplificare la creazione e l'orchestrazione dell'infrastruttura in Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Eseguire la migrazione del carico di lavoro esistente in Azure

Dopo aver usato Ansible per definire l'infrastruttura, è possibile applicare il playbook dell'applicazione in modo da consentire ad Azure di ridimensionare automaticamente l'ambiente in base alle esigenze. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizzare l'applicazione nativa del cloud in Azure

Ansible consente di automatizzare le applicazioni native del cloud in Azure tramite microservizi di Azure quali, ad esempio, [Funzioni di Azure](https://azure.microsoft.com//services/functions/) e [Kubernetes in Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Gestire le distribuzioni con un inventario dinamico
Tramite il relativo [inventario dinamico](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) Ansible consente di eseguire il pull dell'inventario dalle risorse di Azure. È quindi possibile contrassegnare le distribuzioni di Azure esistenti e gestire tali le distribuzioni con tag tramite Ansible.

## <a name="additional-azure-marketplace-options"></a>Opzioni aggiuntive di Azure Marketplace
[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) di Red Hat aiuta le organizzazioni a implementare l'automazione IT e a gestire distribuzioni complesse in infrastrutture fisiche, virtuali e cloud. Ansible Tower include funzionalità che mettono a disposizione i livelli aggiuntivi di visibilità, controllo, sicurezza ed efficienza necessari per le moderne aziende. Ansible Tower crittografa le credenziali, ad esempio le chiavi Azure e SSH, in modo da consentire la delega dei processi a dipendenti meno esperti senza il rischio di esporre le credenziali.

## <a name="next-steps"></a>Passaggi successivi
- [Configurare Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Creare una macchina virtuale Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)