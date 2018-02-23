---
title: "Scalare automaticamente i set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure | Microsoft Docs"
description: "Come creare regole di scalabilità automatica per i set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Scalare automaticamente un set di scalabilità di una macchina virtuale con l'interfaccia della riga di comando di Azure 2.0
Quando si crea un set di scalabilità, definire il numero di istanze di macchine virtuali da eseguire. È possibile aumentare o ridurre automaticamente il numero di istanze di macchine virtuali in base alle richieste dell'applicazione. La scalabilità automatica consente di adattarsi alle esigenze dei clienti o di rispondere alle prestazioni dell'applicazione durante il ciclo di vita dell'app.

Questo articolo illustra come creare regole di scalabilità automatica con l'interfaccia della riga di comando di Azure 2.0 per monitorare le prestazioni delle istanze di macchine virtuali nel set di scalabilità. Queste regole di scalabilità automatica aumentano o diminuiscono il numero di istanze di macchine virtuali in risposta a queste metriche delle prestazioni. È anche possibile completare questa procedura con [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o nel [portale di Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>prerequisiti
Per creare regole di scalabilità automatica, è necessario un set di scalabilità di macchina virtuale esistente. È possibile creare un set di scalabilità con il [portale di Azure](virtual-machine-scale-sets-create-portal.md), l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-create-cli.md) o [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Per rendere più semplice la creazione delle regole di scalabilità automatica, definire alcune variabili per il set di scalabilità. L'esempio seguente definisce le variabili per il set di scalabilità denominato *myScaleSet* nel gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali*. Ottenere l'ID della propria sottoscrizione con [az account show](/cli/azure/account#az_account_show). Se si hanno più sottoscrizioni associate all'account, viene restituita solo la prima sottoscrizione. Modificare i nomi e l'ID sottoscrizione nel modo seguente:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Definire un profilo di scalabilità automatica
Le regole di scalabilità automatica vengono distribuite in formato JSON (JavaScript Object Notation) con l'interfaccia della riga di comando di Azure 2.0. Il file JSON completo che definisce e distribuisce le regole di scalabilità automatica è disponibile più avanti nell'articolo. 

L'inizio del profilo di scalabilità automatica definisce la capacità predefinita, minima e massima del set di scalabilità. L'esempio seguente imposta la capacità predefinita e minima di *2* istanze di macchine virtuali e la capacità massima di *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Creare una regola per l'aumento automatico
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nel set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o il disco, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

È possibile creare una regola che aumenti il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU è superiore al 70% per un periodo di 10 minuti. Quando la regola viene attivata, il numero di istanze di macchine virtuali viene aumentato del 20%. Nei set di scalabilità con un numero minore di istanze di macchine virtuali, è possibile impostare `type` su *ChangeCount* e aumentare `value` di *1* o *2* istanze. Nei set di scalabilità con un numero elevato di istanze di macchine virtuali, potrebbe essere più appropriato un aumento del 10% o del 20% delle istanze di macchine virtuali.

Per questa regola vengono usati i parametri seguenti:

| Parametro         | Spiegazione                                                                                                         | Valore           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | La metrica delle prestazioni da monitorare e a cui applicare azioni dei set di scalabilità.                                                   | CPU percentuale  |
| *timeGrain*       | La frequenza con cui le metriche vengono raccolte per l'analisi.                                                                   | 1 minuto        |
| *timeAggregation* | Definisce la modalità di aggregazione delle metriche raccolte per l'analisi.                                                | Media         |
| *timeWindow*      | Il tempo monitorato prima che vengano confrontati i valori delle metriche e delle soglie.                                   | 10 minuti      |
| *operator*        | Operatore usato per confrontare i dati della metrica rispetto alla soglia.                                                     | Maggiore di    |
| *threshold*       | Il valore che determina l'attivazione di un'azione da parte della regola di scalabilità automatica.                                                      | 70%             |
| *direction*       | Definisce se il set di scalabilità deve aumentare o diminuire quando si applica la regola.                                             | Aumento        |
| *type*            | Indica che il numero di istanze di macchine virtuali deve essere modificato di un valore percentuale.                                 | Modifica percentuale  |
| *value*           | Il numero di istanze di macchine virtuali da aumentare o diminuire quando si applica la regola.                                            | 20              |
| *cooldown*        | Il tempo di attesa prima che la regola venga applicata nuovamente in modo che le azioni di scalabilità automatica diventino effettive. | 5 minuti       |

L'esempio seguente definisce la regola per aumentare il numero di istanze di macchine virtuali. *metricResourceUri* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Creare una regola per la riduzione automatica
Nelle ore serali o nel fine settimana è possibile che la richiesta delle applicazioni si riduca. Se il carico diminuisce in modo coerente nel tempo, è possibile configurare regole di scalabilità automatica per diminuire il numero di istanze di macchine virtuali nel set di scalabilità. Questa azione riduce i costi di esecuzione del set di scalabilità poiché si esegue solo il numero di istanze necessarie per soddisfare la richiesta corrente.

Creare un'altra regola che diminuisca il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU scende sotto il 30% per un periodo di 10 minuti. L'esempio seguente definisce la regola per aumentare il numero di istanze di macchine virtuali. *metricResourceUri* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Applicare le regole di scalabilità automatica a un set di scalabilità
Il passaggio finale consiste nell'applicare il profilo e le regole di scalabilità automatica al set di scalabilità. La scalabilità potrà quindi essere aumentata o diminuita automaticamente in base alla richiesta delle applicazioni. Applicare il profilo di scalabilità automatica con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) come indicato di seguito. Il file JSON completo usa il profilo e le regole descritte nelle sezioni precedenti.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorare il numero di istanze in un set di scalabilità
Per visualizzare il numero e lo stato delle istanze di macchine virtuali, visualizzare un elenco di istanze del set di scalabilità con [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances). Lo stato indica se l'istanza della macchina virtuale esegue il provisioning quando il set di scalabilità viene aumentato automaticamente oppure esegue il deprovisioning quando il set di scalabilità viene ridotto automaticamente. L'esempio seguente visualizza lo stato dell'istanza della macchina virtuale per il set di scalabilità denominato *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Ridimensionare automaticamente in base a una pianificazione
Negli esempi precedenti viene aumentato o ridotto automaticamente un set di scalabilità con le metriche host di base, ad esempio l'utilizzo della CPU. È possibile anche creare regole di scalabilità automatica in base a pianificazioni. Queste regole basate su pianificazione consentono di aumentare il numero di istanze di macchine virtuali in anticipo rispetto all'aumento previsto della richiesta delle applicazioni, ad esempio nelle ore di lavoro intenso, e quindi di ridurre automaticamente il numero di istanze quando si prevede una riduzione della richiesta, ad esempio nel fine settimana.

Per usare regole di scalabilità automatica basate su pianificazione, creare un profilo JSON che definisce il numero di istanze di macchine virtuali da eseguire per un intervallo di tempo con ora di inizio e fine fissate. L'esempio seguente definisce una regola per l'aumento a *10* istanze alle *9* di ogni giorno lavorativo (da lunedì a venerdì).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Per la riduzione durante la sera, creare un'altra regola che specifica un numero minore di istanze di macchine virtuali e un'ora di inizio appropriata.

L'esempio completo seguente definisce le regole per l'aumento e la riduzione, quindi applica il profilo di scalabilità automatica con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Questo esempio sovrascrive le regole di scalabilità automatica basate sulle metriche create negli esempi precedenti. *metricResourceUri* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare le regole di scalabilità automatica per scalare orizzontalmente e aumentare o diminuire il *numero* di istanze di macchine virtuali nel set di scalabilità. È possibile anche scalare verticalmente per aumentare o diminuire le *dimensioni* delle istanze di macchine virtuali. Per altre informazioni, vedere [Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Per informazioni su come gestire le istanze di macchine virtuali, vedere [Gestire set di scalabilità di macchine virtuali con Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Per informazioni su come generare avvisi all'attivazione delle regole di scalabilità automatica, vedere [Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Monitoraggio di Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Vedere anche [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).
