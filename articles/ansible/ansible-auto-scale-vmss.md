---
title: Esercitazione - Ridimensionare automaticamente i set di scalabilità di macchine virtuali in Azure con Ansible
description: Informazioni su come usare Ansible per ridimensionare set di scalabilità di macchine virtuali con scalabilità automatica in Azure
keywords: ansible, azure, devops, bash, playbook, scalabilità, scalabilità automatica, macchina virtuale, set di scalabilità di macchine virtuali, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156822"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Esercitazione: Ridimensionare automaticamente i set di scalabilità di macchine virtuali in Azure con Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

La funzionalità per adattare automaticamente il numero di istanze di macchine virtuali è denominata [scalabilità automatica](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Il vantaggio della scalabilità automatica è che riduce il sovraccarico di gestione per monitorare e ottimizzare le prestazioni dell'applicazione. È possibile configurare la scalabilità automatica in risposta a una richiesta o una pianificazione definita. Con Ansible è possibile specificare le regole di scalabilità automatica che definiscono le prestazioni accettabili per un'esperienza positiva del cliente.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definire un profilo di scalabilità automatica
> * Ridimensionare automaticamente in base a una pianificazione ricorrente
> * Ridimensionare automaticamente in base alle prestazioni delle app
> * Recuperare informazioni sulle impostazioni di scalabilità automatica 
> * Disabilitare un'impostazione di scalabilità automatica

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Ridimensionare automaticamente in base a una pianificazione

Per abilitare la scalabilità automatica su un set di scalabilità, è innanzitutto necessario definire un profilo di scalabilità automatica. Questo profilo definisce la capacità predefinita, minima e massima del set di scalabilità. Questi limiti consentono di controllare i costi perché le istanze di macchine virtuali non vengono create di continuo. Permettono anche di trovare un equilibrio appropriato tra prestazioni e numero minimo di istanze che rimangono in un evento di riduzione. 

Ansible consente di ridimensionare i set di scalabilità in base a una data specifica o a una pianificazione ricorrente.

Il codice del playbook in questa sezione consente di aumentare a tre il numero di istanze delle macchine virtuali alle 10 del mattino di ogni lunedì.

Salvare il playbook seguente come `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Ridimensionare automaticamente in base ai dati delle prestazioni

Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nei set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. Ansible consente di controllare le metriche da monitorare, ad esempio l'utilizzo della CPU, l'utilizzo del disco e il tempo di caricamento delle app. È possibile aumentare e ridurre il numero di istanze nei set di scalabilità in base ai valori soglia per le metriche delle prestazioni, a una pianificazione ricorrente oppure a una data specifica. 

Il codice del playbook in questa sezione consente di controllare il carico di lavoro della CPU nei 10 minuti che precedono le 18 di ogni lunedì. 

In base alle metriche delle prestazioni della CPU, il playbook esegue una delle azioni seguenti:

- Aumenta a quattro il numero di istanze delle macchine virtuali
- Riduce a uno il numero di istanze delle macchine virtuali

Salvare il playbook seguente come `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
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

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Ottenere informazioni sulle impostazioni di scalabilità automatica 

Il codice del playbook in questa sezione usa il modulo `azure_rm_autoscale_facts` per recuperare i dettagli dell'impostazione di scalabilità automatica.

Salvare il playbook seguente come `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Disabilitare le impostazioni di scalabilità automatica

È possibile disabilitare le impostazioni di scalabilità automatica in due modi. Un modo consiste nel modificare il valore della chiave `enabled` da `true` a `false`. Il secondo modo consiste nell'eliminare l'impostazione.

Il codice del playbook in questa sezione consente di eliminare l'impostazione di scalabilità automatica. 

Salvare il playbook seguente come `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Esercitazione: Aggiornare l'immagine personalizzata di set di scalabilità di macchine virtuali di Azure tramite Ansible](./ansible-vmss-update-image.md)