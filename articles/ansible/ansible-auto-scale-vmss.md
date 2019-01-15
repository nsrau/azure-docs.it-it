---
title: Ridimensionare automaticamente un set di scalabilità di macchine virtuali in Azure con Ansible
description: Informazioni su come usare Ansible per ridimensionare un set di scalabilità di macchine virtuali con scalabilità automatica in Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, scalabilità, scalabilità automatica, macchina virtuale, set di scalabilità di macchine virtuali, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 7721dba37131616122f8a5a902e3c63de5c7157f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157055"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Ridimensionare automaticamente un set di scalabilità di macchine virtuali in Azure con Ansible
Ansible consente di automatizzare la distribuzione e la configurazione delle risorse nell'ambiente in uso. È possibile usare Ansible per gestire il set di scalabilità di macchine virtuali in Azure, così come si farebbe con qualsiasi altra risorsa di Azure. 

Quando si crea un set di scalabilità, definire il numero di istanze di macchine virtuali da eseguire. È possibile aumentare o ridurre automaticamente il numero di istanze di macchine virtuali in base alle richieste dell'applicazione. La scalabilità automatica consente di adattarsi alle esigenze dei clienti o di rispondere alle prestazioni dell'applicazione durante il ciclo di vita dell'app. In questo articolo si creerà un'impostazione di scalabilità automatica e lo si assocerà a un set di scalabilità di macchine virtuali esistente. Nell'impostazione di scalabilità automatica è possibile configurare una regola per aumentare o ridurre il numero di istanze come desiderato.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Un set di scalabilità di macchine virtuali di Azure esistente. - Se non è disponibile, [creare un set di scalabilità di macchine virtuali in Azure con Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> In questa esercitazione, per eseguire i playbook di esempio seguenti è necessario Ansible 2.7. 

## <a name="auto-scale-based-on-a-schedule"></a>Scalabilità automatica in base a una pianificazione   
Per abilitare la scalabilità automatica su un set di scalabilità, è innanzitutto necessario definire un profilo di scalabilità automatica. Questo profilo definisce la capacità predefinita, minima e massima del set di scalabilità. Questi limiti consentono di controllare i costi perché le istanze di macchine virtuali non vengono create di continuo. Permettono anche di trovare un equilibrio appropriato tra prestazioni e numero minimo di istanze che rimangono in un evento di riduzione. 

È possibile aumentare e ridurre il numero di istanze dei set di scalabilità di macchine virtuali in base a una pianificazione ricorrente oppure a una data specifica. In questa sezione viene presentato un playbook di Ansible di esempio che crea un'impostazione di scalabilità automatica che aumenta a tre il numero di istanze di macchine virtuali nei set di scalabilità alle 10:00 di ogni lunedì, fuso orario del Pacifico. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Salvare il playbook come *vmss-auto-scale.yml*. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Scalabilità automatica in base ai dati delle prestazioni
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nei set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o il disco, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

È possibile aumentare e ridurre il numero di istanze nei set di scalabilità di macchine virtuali in base ai valori soglia per le metriche delle prestazioni, a una pianificazione ricorrente oppure a una data specifica. In questa sezione viene presentato un playbook di Ansible di esempio che controlla il carico di lavoro negli ultimi 10 minuti alle 18.00 di ogni lunedì, fuso orario del Pacifico, e aumenta a quattro o riduce a uno il numero di istanze di macchine virtuali nei set di scalabilità in base alla metrica della percentuale di utilizzo della CPU. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Salvare il playbook come *vmss-auto-scale-metrics.yml*. Per eseguire il playbook Ansible, usare il comando **ansible-playbook** come segue:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Ottenere informazioni sulle impostazioni di scalabilità automatica esistenti
È possibile ottenere i dettagli di qualsiasi impostazione di scalabilità automatica tramite il modulo *azure_rm_autoscale_facts* con il playbook come segue:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Disabilitare le impostazioni di scalabilità automatica
È possibile disabilitare le impostazioni di scalabilità automatica cambiando `enabled: true` in `enabled: false` o eliminando le impostazioni di scalabilità automatica con il playbook come segue:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Playbook Ansible di esempio per i set di scalabilità di macchine virtuali](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)