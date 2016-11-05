---
title: Configurazione della scalabilità automatica di un servizio cloud nel portale | Microsoft Docs
description: (classico) Informazioni su come usare il portale classico per configurare le regole di scalabilità automatica per un ruolo Web o un ruolo di lavoro del servizio cloud in Azure.
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

Nella pagina Ridimensiona del portale di Azure classico è possibile ridimensionare manualmente un ruolo Web o un ruolo di lavoro oppure abilitare la scalabilità automatica in base al carico della CPU o a una coda di messaggi.

> [!NOTE]
> Questo articolo è incentrato sui ruoli Web e di lavoro del servizio cloud. Quando si crea una macchina virtuale (distribuzione classica) direttamente, questa viene ospitata in un servizio cloud. Alcune delle presenti informazioni si applicano a questi tipi di macchine virtuali. Scalare un set di disponibilità di macchine virtuali significa semplicemente attivarle e disattivarle in base alle regole di scalabilità configurate. Per altre informazioni sulle macchine virtuali e sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali](../virtual-machines/virtual-machines-windows-classic-configure-availability.md).
> 
> 

Prima di configurare la scalabilità per l'applicazione, tenere presente quanto segue:

* La scalabilità è influenzata dall'utilizzo di core. Le istanze del ruolo più ampie usano più core. È possibile ridimensionare il numero di istanze di un'applicazione solo entro i limiti di core previsti dalla sottoscrizione. Ad esempio, se la sottoscrizione prevede al massimo venti core e si esegue un'applicazione con due servizi cloud di medie dimensioni (per un totale di quattro core), l'aumento di istanze di altre distribuzioni del servizio cloud nella sottoscrizione è limitato a sedici core. Per altre informazioni, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md).
* È necessario creare una coda e associarla a un ruolo prima di ridimensionare il numero di istanze di un'applicazione in base a una soglia di messaggi. Per altre informazioni, vedere [Come utilizzare il servizio di archiviazione di accodamento](../storage/storage-dotnet-how-to-use-queues.md).
* È possibile scalare le risorse collegate al servizio cloud. Per altre informazioni sul collegamento di risorse, vedere [Procedura: Collegare una risorsa a un servizio cloud](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).
* Per abilitare la disponibilità elevata dell'applicazione, è necessario accertarsi che sia distribuita con due o più istanze del ruolo. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## Pianificazione della scalabilità
Per impostazione predefinita, i ruoli non seguono una pianificazione specifica. Per questo motivo, tutte le impostazioni modificate vengono applicate in qualsiasi momento e tutti i giorni dell'anno. Se necessario, è possibile configurare la scalabilità manuale o automatica per:

* Giorni della settimana
* Fine settimana
* Ore notturne
* Ore diurne
* Date specifiche
* Intervalli di date specifiche

È possibile impostare questa configurazione nel [portale di Azure classico](https://manage.windowsazure.com/) nella pagina **Servizi cloud** > **[nome del servizio cloud]** > **Ridimensiona** > **[Produzione o Gestione temporanea]**.

Fare clic sul pulsante **Imposta ore di pianificazione** per ogni ruolo che si desidera modificare.

![Ridimensionamento automatico del servizio cloud in base a una pianificazione][scale_schedules]

## Scalabilità manuale
Nella pagina **Ridimensiona** è possibile aumentare o diminuire manualmente il numero delle istanze in esecuzione in un servizio cloud. Questa configurazione è prevista per qualsiasi pianificazione creata o, in mancanza di una pianificazione, viene applicata sempre.

1. Nel [portale di Azure classico](https://manage.windowsazure.com/) fare clic su **Servizi cloud** e quindi sul nome del servizio cloud per aprire il dashboard.
   
   > [!TIP]
   > Se il servizio cloud non viene visualizzato, potrebbe essere necessario passare da **Produzione** a **Gestione temporanea** o viceversa.
   > 
   > 
2. Fare clic su **Scale**.
3. Selezionare la pianificazione di cui si desidera modificare le opzioni di scalabilità. In assenza di una pianificazione definita, l'impostazione predefinita è *Nessuna ora pianificata*.
4. Trovare la sezione **Ridimensiona in base alla metrica** e selezionare **Nessuna**. Questa è l'impostazione predefinita per tutti i ruoli.
5. In ogni ruolo nel servizio cloud è presente un dispositivo di scorrimento che consente di modificare il numero di istanze da usare.
   
    ![Ridimensionare manualmente un ruolo del servizio cloud][manual_scale]
   
    Se sono richieste altre istanze, potrebbe essere necessario modificare le [dimensioni di macchine virtuali e di servizi cloud](cloud-services-sizes-specs.md).
6. Fare clic su **Save**. Verranno aggiunte o rimosse istanze del ruolo in base alle selezioni effettuate.

> [!TIP]
> Per informazioni sulla funzione di una specifica impostazione, spostare il mouse sull'icona ![][tip_icon] ogni volta che viene visualizzata.
> 
> 

## Scalabilità automatica - CPU
Questa configurazione è possibile se la percentuale media di uso della CPU supera o scende al di sotto delle soglie specificate. Vengono create o eliminate istanze del ruolo.

1. Nel [portale di Azure classico](https://manage.windowsazure.com/) fare clic su **Servizi cloud** e quindi sul nome del servizio cloud per aprire il dashboard.
   
   > [!TIP]
   > Se il servizio cloud non viene visualizzato, potrebbe essere necessario passare da **Produzione** a **Gestione temporanea** o viceversa.
   > 
   > 
2. Fare clic su **Scale**.
3. Selezionare la pianificazione di cui si desidera modificare le opzioni di scalabilità. In assenza di una pianificazione definita, l'impostazione predefinita è *Nessuna ora pianificata*.
4. Trovare la sezione **Ridimensiona in base alla metrica** e selezionare **CPU**.
5. A questo punto è possibile configurare un intervallo minimo e massimo di istanze dei ruoli, l'intervallo di destinazione per l'uso della CPU (per attivare un aumento) e il numero di istanze da aumentare o ridurre.

![Ridimensionare un ruolo del servizio cloud per carico CPU][cpu_scale]

> [!TIP]
> Per informazioni sulla funzione di una specifica impostazione, spostare il mouse sull'icona ![][tip_icon] ogni volta che viene visualizzata.
> 
> 

## Scalabilità automatica - Coda
Questa configurazione automatica è possibile se il numero di messaggi in una coda supera o scende al di sotto di una soglia specificata. Vengono create o eliminate istanze del ruolo.

1. Nel [portale di Azure classico](https://manage.windowsazure.com/) fare clic su **Servizi cloud** e quindi sul nome del servizio cloud per aprire il dashboard.
   
   > [!TIP]
   > Se il servizio cloud non viene visualizzato, potrebbe essere necessario passare da **Produzione** a **Gestione temporanea** o viceversa.
   > 
   > 
2. Fare clic su **Scale**.
3. Trovare la sezione **Ridimensiona in base alla metrica** e selezionare **CPU**.
4. A questo punto è possibile configurare un intervallo minimo e massimo di istanze dei ruoli, la coda, la quantità di messaggi in coda da elaborare per ogni istanza e il numero di istanze da aumentare o ridurre.

![Ridimensionare un ruolo del servizio cloud per coda di messaggi][queue_scale]

> [!TIP]
> Per informazioni sulla funzione di una specifica impostazione, spostare il mouse sull'icona ![][tip_icon] ogni volta che viene visualizzata.
> 
> 

## Scalare risorse collegate
Quando si scala un ruolo, spesso risulta utile scalare anche il database utilizzato dall'applicazione. Se si collega il database al servizio cloud, è possibile accedere alle impostazioni di scalabilità per tale risorsa facendo clic sul collegamento appropriato.

1. Nel [portale di Azure classico](https://manage.windowsazure.com/) fare clic su **Servizi cloud** e quindi sul nome del servizio cloud per aprire il dashboard.
   
   > [!TIP]
   > Se il servizio cloud non viene visualizzato, potrebbe essere necessario passare da **Produzione** a **Gestione temporanea** o viceversa.
   > 
   > 
2. Fare clic su **Scale**.
3. Trovare la sezione delle **risorse collegate** e fare clic su **Gestire la scalabilità per questo database**.
   
   > [!NOTE]
   > Se la sezione delle **risorse collegate** non viene visualizzata, è probabile che non sia presente alcuna risorsa.
   > 
   > 

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png

<!---HONumber=AcomDC_0914_2016-->