<properties
   pageTitle="Problemi di riavvio o ridimensionamento della VM | Microsoft Azure"
   description="Risolvere i problemi della distribuzione classica con il riavvio e il ridimensionamento di una macchina virtuale Windows esistente in Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>  

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>  

# Risolvere i problemi della distribuzione classica con il riavvio e il ridimensionamento di una macchina virtuale Windows esistente in Azure

> [AZURE.SELECTOR]
- [Classico](../articles/virtual-machines/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
- [Gestione risorse](../articles/virtual-machines/virtual-machines-windows-restart-resize-error-troubleshooting.md)

Quando si prova ad avviare una macchina virtuale (VM) di Azure arrestata o se ne ridimensiona una esistente, l'errore comune che si verifica è un errore di allocazione. L'errore si verifica quando il cluster o l'area non ha risorse disponibili o non può supportare le dimensioni della VM richieste.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Raccogliere log di controllo

Per avviare la risoluzione dei problemi, raccogliere i log di controllo per identificare l'errore associato al problema.

Nel portale di Azure fare clic su **Esplora** > **Macchine virtuali** > _macchina virtuale Windows personale_ > **Impostazioni** > **Log di controllo**.

## Problema: Errore durante l'avvio di una VM arrestata

Si prova ad avviare una VM arrestata ma viene visualizzato un errore di allocazione.

### Causa

La richiesta di avvio della VM arrestata deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non ha spazio disponibile per soddisfare la richiesta.

### Risoluzione

* Creare un nuovo servizio cloud e associarlo a un'area o una rete virtuale basata sull'area, ma non a un gruppo di affinità.

* Eliminare la VM arrestata.

* Ricreare la VM nel nuovo servizio cloud usando i dischi.

* Avviare la macchina virtuale ricreata.

Se si verifica un errore durante il tentativo di creare un nuovo servizio cloud, riprovare in un secondo momento o cambiare l'area per il servizio cloud.

> [AZURE.IMPORTANT] Il nuovo servizio cloud avrà un nuovo nome e un indirizzo VIP, quindi si dovranno modificare tali informazioni per tutte le dipendenze che usano queste informazioni per il servizio cloud esistente.

## Problema: Errore durante il ridimensionamento di una VM esistente

Si prova a ridimensionare una VM esistente ma viene visualizzato un errore di allocazione.

### Causa

La richiesta di ridimensionamento della VM deve essere eseguita nel cluster originale che ospita il servizio cloud. Tuttavia, il cluster non supporta le dimensioni della VM richieste.

### Risoluzione

Ridurre le dimensioni della VM richieste e quindi ripetere la richiesta di ridimensionamento.

* Fare clic su **Esplora tutto** > **Macchine virtuali (classico)** > _macchina virtuale personale_ > **Impostazioni** > **Dimensioni**. Per i passaggi dettagliati, vedere l'articolo relativo al [Ridimensionamento della macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx).

Se non è possibile ridurre le dimensioni della VM, seguire questi passaggi:

  * Creare un nuovo servizio cloud, verificando che non sia collegato a un gruppo di affinità e non sia associato a una rete virtuale collegata a un gruppo di affinità.

  * Nel servizio creare una nuova VM con dimensioni maggiori.

È possibile consolidare tutte le VM nello stesso servizio cloud. Se il servizio cloud esistente è associato a una rete virtuale basata sull'area, è possibile connetterlo alla rete virtuale esistente.

Se il servizio cloud esistente non è associato a una rete virtuale basata sull'area, è necessario eliminare le VM nel servizio cloud esistente e ricrearle nel nuovo servizio cloud dai relativi dischi. È tuttavia importante ricordare che il nuovo servizio cloud avrà un nuovo nome e un nuovo indirizzo VIP, quindi sarà necessario aggiornarli per tutte le dipendenze che attualmente usano queste informazioni per il servizio cloud esistente.

## Passaggi successivi

Se si verificano problemi durante la creazione di una nuova VM Windows in Azure, vedere [Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Windows in Azure](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md).

<!---HONumber=AcomDC_0921_2016-->