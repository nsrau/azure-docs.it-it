---
title: Configurazione della scalabilità automatica di un servizio cloud nel portale | Microsoft Docs
description: Informazioni su come usare il portale per configurare le regole di scalabilità automatica per un ruolo Web o un ruolo di lavoro del servizio cloud in Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo

---
# Come configurare la scalabilità automatica di un servizio cloud
> [!div class="op_single_selector"]
> * [Portale di Azure](cloud-services-how-to-scale-portal.md)
> * [Portale di Azure classico](cloud-services-how-to-scale.md)
> 
> 

È possibile impostare condizioni per un ruolo di lavoro del servizio cloud che attivano operazioni di scalabilità verticale o orizzontale. Le condizioni per il ruolo possono essere basate sulla CPU, sul disco o sul carico di rete del ruolo. È anche possibile impostare una condizione in base a una coda di messaggi o alla metrica di un'altra risorsa di Azure associata alla sottoscrizione.

> [!NOTE]
> Questo articolo è incentrato sui ruoli Web e di lavoro del servizio cloud. Quando si crea una macchina virtuale (distribuzione classica) direttamente, questa viene ospitata in un servizio cloud. È possibile ridimensionare una macchina virtuale standard tramite l'associazione con un [set di disponibilità](../virtual-machines/virtual-machines-windows-classic-configure-availability.md) e attivarla o disattivarla manualmente.
> 
> 

## Considerazioni
Prima di configurare la scalabilità per l'applicazione, tenere presente quanto segue:

* La scalabilità è influenzata dall'utilizzo di core. Le istanze del ruolo più ampie usano più core. È possibile ridimensionare il numero di istanze di un'applicazione solo entro i limiti di core previsti dalla sottoscrizione. Ad esempio, se la sottoscrizione prevede al massimo venti core e si esegue un'applicazione con due servizi cloud di medie dimensioni (per un totale di quattro core), l'aumento di istanze di altre distribuzioni del servizio cloud nella sottoscrizione è limitato a sedici core. Per altre informazioni, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md).
* È possibile eseguire la scalabilità in base a una soglia di messaggi in coda. Per altre informazioni sull'uso delle code, vedere l'articolo relativo all'[uso del servizio di archiviazione code](../storage/storage-dotnet-how-to-use-queues.md).
* È anche possibile ridimensionare altre risorse associate alla sottoscrizione.
* Per abilitare la disponibilità elevata dell'applicazione, è necessario accertarsi che sia distribuita con due o più istanze del ruolo. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## Posizione della scalabilità
Dopo aver selezionato il servizio cloud, viene visualizzato il pannello del servizio cloud.

1. Nel pannello del servizio cloud, nel riquadro **Ruoli e istanze**, selezionare il nome del servizio cloud. **IMPORTANTE**: assicurarsi di selezionare il ruolo del servizio cloud, non l'istanza del ruolo che si trova sotto il ruolo.
   
    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selezionare il riquadro **Ridimensiona**.
   
    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## Scalabilità automatica
È possibile configurare le impostazioni di scalabilità per un ruolo scegliendo tra due modalità: **manuale** o **automatica**. Con la modalità manuale, come si può immaginare, si imposta il numero assoluto di istanze. La modalità automatica consente tuttavia di impostare regole che determinano il modo e la dimensione della scalabilità.

Impostare l'opzione **Ridimensiona di** su **regole per la pianificazione e le prestazioni**.

![Impostazioni di scalabilità dei servizi cloud con profilo e regola](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Un profilo esistente.
2. Aggiungere una regola per il profilo padre.
3. Aggiungere un altro profilo.

Selezionare **Aggiungi profilo**. Il profilo determina la modalità che si desidera usare per la scalabilità: **sempre**, **ricorrenza**, **data fissa**.

Dopo aver configurato il profilo e le regole, selezionare l'icona **Salva** nella parte superiore.

#### Profilo
Il profilo imposta istanze minime e massime per la scalabilità, anche quando è attivo questo intervallo di scalabilità.

* **Sempre**
  
    Consente di mantenere sempre disponibile questo intervallo di istanze.
  
    ![Servizio cloud che esegue sempre la scalabilità](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Ricorrenza**
  
    Consente di scegliere un set di giorni della settimana per la scalabilità.
  
    ![Scalabilità del servizio cloud con pianificazione ricorrente](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Data fissa**
  
    Un intervallo di date fisso per eseguire la scalabilità del ruolo.
  
    ![Scalabilità del servizio cloud con data fissa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Dopo aver configurato il profilo, selezionare il pulsante **OK** nella parte inferiore del pannello del profilo.

#### Regola
Le regole vengono aggiunte al profilo e rappresentano la condizione che attiva la scalabilità.

Il trigger della regola è basato su una metrica del servizio cloud (uso della CPU, attività del disco o attività di rete) a cui è possibile aggiungere un valore condizionale. È anche possibile impostare il trigger in base a una coda di messaggi o alla metrica di un'altra risorsa di Azure associata alla sottoscrizione.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Dopo aver configurato la regola, selezionare il pulsante **OK** nella parte inferiore del pannello della regola.

## Ritorno alla scalabilità manuale
Accedere a [Impostazioni scalabilità](#where-scale-is-located) e impostare l'opzione **Ridimensiona di** su **numero di istanze immesso manualmente**.

![Impostazioni di scalabilità dei servizi cloud con profilo e regola](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Questa operazione rimuove la scalabilità automatica dal ruolo e quindi è possibile impostare direttamente il numero di istanze.

1. L'opzione di scalabilità (manuale o automatica).
2. Un dispositivo di scorrimento delle istanze del ruolo per impostare le istanze da ridimensionare.
3. Istanze del ruolo da ridimensionare.

Dopo aver configurato il profilo e le regole, selezionare l'icona **Salva** nella parte superiore.

<!---HONumber=AcomDC_0914_2016-->