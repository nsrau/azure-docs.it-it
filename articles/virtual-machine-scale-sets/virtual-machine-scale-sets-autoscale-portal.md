---
title: Ridimensionare automaticamente i set di scalabilità di macchine virtuali nel portale di Azure | Microsoft Docs
description: Come creare regole di scalabilità automatica per i set di scalabilità di macchine virtuali nel portale di Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 984b16dae26fb6d9d33ef68ac3e8c8b658e82e08
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Ridimensionare automaticamente un set di scalabilità di macchine virtuali nel portale di Azure
Quando si crea un set di scalabilità, definire il numero di istanze di macchine virtuali da eseguire. È possibile aumentare o ridurre automaticamente il numero di istanze di macchine virtuali in base alle richieste dell'applicazione. La scalabilità automatica consente di adattarsi alle esigenze dei clienti o di rispondere alle prestazioni dell'applicazione durante il ciclo di vita dell'app.

Questo articolo illustra come creare regole di scalabilità automatica nel portale di Azure per monitorare le prestazioni delle istanze di macchine virtuali nel set di scalabilità. Queste regole di scalabilità automatica aumentano o diminuiscono il numero di istanze di macchine virtuali in risposta a queste metriche delle prestazioni. È anche possibile completare questa procedura con [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md).


## <a name="prerequisites"></a>prerequisiti
Per creare regole di scalabilità automatica, è necessario un set di scalabilità di macchina virtuale esistente. È possibile creare un set di scalabilità con il [portale di Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) o l'[interfaccia della riga di comando di Azure 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Creare una regola per l'aumento automatico
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nel set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o il disco, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

1. Aprire il portale di Azure e selezionare **Gruppi di risorse** nel menu sul lato sinistro del dashboard.
2. Selezionare il gruppo di risorse che contiene il set di scalabilità, quindi scegliere il set di scalabilità nell'elenco di risorse.
3. Scegliere **Proporzioni** dal menu nella parte sinistra della finestra del set di scalabilità. Selezionare il pulsante di **Abilita scalabilità automatica**:

    ![Abilitare la scalabilità automatica nel portale di Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Immettere un nome per le impostazioni, ad esempio *scalabilità automatica*, quindi selezionare l'opzione per **aggiungere una regola**.

5. È possibile creare una regola che aumenti il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU è superiore al 70% per un periodo di 10 minuti. Quando la regola viene attivata, il numero di istanze di macchine virtuali viene aumentato del 20%. Nei set di scalabilità con un numero ridotto di istanze di macchine virtuali è possibile impostare **Operazione** su *Aumenta numero di* e quindi specificare *1* o *2* per *Conteggio istanze*. Nei set di scalabilità con un numero elevato di istanze di macchine virtuali, potrebbe essere più appropriato un aumento del 10% o del 20% delle istanze di macchine virtuali.

    Specificare le seguenti impostazioni per la regola:
    
    | Parametro              | Spiegazione                                                                                                         | Valore          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Aggregazione temporale*     | Definisce la modalità di aggregazione delle metriche raccolte per l'analisi.                                                | Media        |
    | *Nome della metrica*          | La metrica delle prestazioni da monitorare e a cui applicare azioni dei set di scalabilità.                                                   | CPU percentuale |
    | *Statistica intervallo di tempo* | Definisce la modalità di aggregazione delle metriche raccolte in ogni intervallo di tempo per l'analisi.                             | Media        |
    | *Operatore*             | Operatore usato per confrontare i dati della metrica rispetto alla soglia.                                                     | Maggiore di   |
    | *Soglia*            | La percentuale che determina l'attivazione di un'azione da parte della regola di scalabilità automatica.                                                 | 70             |
    | *Duration*             | Il tempo monitorato prima che vengano confrontati i valori delle metriche e delle soglie.                                   | 10 minuti     |
    | *Operazione*            | Definisce se il set di scalabilità deve aumentare o diminuire quando si applica la regola e quale incremento usare                        | Aumenta percentuale di |
    | *Numero di istanze*       | La percentuale di istanze di macchine virtuali deve essere modificata quando viene attivata la regola.                                            | 20             |
    | *Disattiva regole dopo (minuti)*  | Il tempo di attesa prima che la regola venga applicata nuovamente in modo che le azioni di scalabilità automatica diventino effettive. | 5 minuti      |

    Gli esempi seguenti mostrano una regola creata nel portale di Azure che corrisponde a queste impostazioni:    

    ![Creare una regola di scalabilità automatica per aumentare il numero di istanze di macchine virtuali](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Per creare la regola, selezionare **Aggiungi**


## <a name="create-a-rule-to-automatically-scale-in"></a>Creare una regola per la riduzione automatica
Nelle ore serali o nel fine settimana è possibile che la richiesta delle applicazioni si riduca. Se il carico diminuisce in modo coerente nel tempo, è possibile configurare regole di scalabilità automatica per diminuire il numero di istanze di macchine virtuali nel set di scalabilità. Questa azione riduce i costi di esecuzione del set di scalabilità poiché si esegue solo il numero di istanze necessarie per soddisfare la richiesta corrente.

1. Scegliere di nuovo **Aggiungi una regola**.
2. Creare una regola che diminuisca il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU scende sotto il 30% per un periodo di 10 minuti. Quando la regola viene attivata, il numero di istanze di macchine virtuali viene diminuito del 20%.

    Usare lo stesso approccio usato con la regola precedente. Modificare le seguenti impostazioni per la regola:
    
    | Parametro              | Spiegazione                                                                                                          | Valore          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operatore*             | Operatore usato per confrontare i dati della metrica rispetto alla soglia.                                                      | Minore di   |
    | *Soglia*            | La percentuale che determina l'attivazione di un'azione da parte della regola di scalabilità automatica.                                                 | 30             |
    | *Operazione*            | Definisce se il set di scalabilità deve aumentare o diminuire quando si applica la regola e quale incremento usare                         | Riduci percentuale di |
    | *Numero di istanze*       | La percentuale di istanze di macchine virtuali deve essere modificata quando viene attivata la regola.                                             | 20             |

3. Per creare la regola, selezionare **Aggiungi**


## <a name="define-autoscale-instance-limits"></a>Definire i limiti di scalabilità automatica delle istanze
Il profilo di scalabilità automatica deve definire un numero minimo e massimo e un numero predefinito di istanze di macchine virtuali. Quando vengono applicate le regole di scalabilità automatica, questi limiti delle istanze assicurano che l'aumento non superi il numero massimo di istanze o che la riduzione non superi il valore minimo di istanze.

1. Impostare i limiti delle istanze seguenti:

    | Minima | Massima | Predefinito|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Per applicare le regole di scalabilità automatica e i limiti delle istanze, selezionare **Salva**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorare il numero di istanze in un set di scalabilità
Per esaminare il numero e lo stato delle istanze di macchine virtuali, selezionare **Istanze** dal menu nella parte sinistra della finestra del set di scalabilità. Lo stato indica se l'istanza della macchina virtuale è in fase di *creazione* poiché il set di scalabilità esegue automaticamente l'aumento oppure di *eliminazione* poiché viene eseguita automaticamente la riduzione.

![Visualizzare un elenco di istanze di macchine virtuali del set di scalabilità](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Ridimensionare automaticamente in base a una pianificazione
Negli esempi precedenti viene aumentato o ridotto automaticamente un set di scalabilità con le metriche host di base, ad esempio l'utilizzo della CPU. È possibile anche creare regole di scalabilità automatica in base a pianificazioni. Queste regole basate su pianificazione consentono di aumentare il numero di istanze di macchine virtuali in anticipo rispetto all'aumento previsto della richiesta delle applicazioni, ad esempio nelle ore di lavoro intenso, e quindi di ridurre automaticamente il numero di istanze quando si prevede una riduzione della richiesta, ad esempio nel fine settimana.

1. Scegliere **Proporzioni** dal menu nella parte sinistra della finestra del set di scalabilità. Per eliminare le regole di scalabilità automatica create negli esempi precedenti, scegliere l'icona del cestino.

    ![Eliminare le regole di scalabilità automatica esistenti](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Scegliere **Add a scale condition** (Aggiungi una condizione di scalabilità). Selezionare l'icona della matita accanto al nome della regola e specificare un nome, ad esempio *Scalabilità orizzontale durante ogni giorno lavorativo*.

    ![Rinominare la regola predefinita di scalabilità automatica](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selezionare il pulsante di opzione **Ridimensiona in base a un numero di istanze specifico**.
4. Per aumentare il numero di istanze, immettere *10* come numero di istanze.
5. Selezionare **Ripeti in giorni specifici** per il tipo **Pianificazione**.
6. Selezionare tutti i giorni lavorativi, dal lunedì al venerdì.
7. Scegliere il fuso orario appropriato, quindi specificare come **ora di inizio** il valore *09:00*.
8. Scegliere di nuovo **Add a scale condition** (Aggiungi una condizione di scalabilità). Ripetere il processo per creare una pianificazione denominata *Riduzione nelle ore serali* che ridimensiona il numero di istanze a *3*, viene ripetuta ogni giorno feriale e inizia alle *18:00*.
9. Per applicare regole di scalabilità automatica basate su pianificazione, selezionare **Salva**.

    ![Creare regole di scalabilità automatica basate su una pianificazione](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Per visualizzare la modalità in cui vengono applicate le regole di scalabilità automatica, selezionare **Cronologia di esecuzione** nella parte superiore della finestra **Proporzioni**. Il grafo e gli eventi elencati mostrano quando si attivano le regole di scalabilità automatica e il numero di istanze di macchine virtuali nel set di scalabilità aumenta o diminuisce.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare le regole di scalabilità automatica per scalare orizzontalmente e aumentare o diminuire il *numero* di istanze di macchine virtuali nel set di scalabilità. È possibile anche scalare verticalmente per aumentare o diminuire le *dimensioni* delle istanze di macchine virtuali. Per altre informazioni, vedere [Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Per informazioni su come gestire le istanze di macchine virtuali, vedere [Gestire set di scalabilità di macchine virtuali con Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Per informazioni su come generare avvisi all'attivazione delle regole di scalabilità automatica, vedere [Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Monitoraggio di Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Vedere anche [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).
