<properties 
   pageTitle="Applicare aggiornamenti a StorSimple Virtual Array | Microsoft Azure"
   description="Viene illustrato come usare l'interfaccia utente Web di StorSimple Virtual Array per applicare aggiornamenti e hotfix"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# Applicare aggiornamenti e hotfix all'array virtuale StorSimple

## Panoramica

A volte è necessario applicare aggiornamenti software o hotfix per mantenere StorSimple Virtual Array sempre aggiornato. In generale, si consiglia di installare gli aggiornamenti tramite il portale di Azure classico. Tuttavia, nei casi in cui il portale non sia disponibile, è possibile usare l'interfaccia utente Web locale per applicare hotfix o aggiornamenti. Questa esercitazione illustra come usare l'interfaccia utente Web locale per applicare aggiornamenti o hotfix. (Per le procedure di installazione di aggiornamenti tramite il portale classico, andare a [Installare gli aggiornamenti regolari tramite il portale di Azure classico](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).)

Tenere presente che l'installazione di un aggiornamento o un hotfix può riavviare il dispositivo. Dato che StorSimple Virtual Array è un dispositivo a nodo singolo, verranno interrotti tutti gli IO in corso e il dispositivo attraverserà un momento di inattività. Prima di applicare un aggiornamento, si consiglia anche di portare offline i volumi o le condivisioni, prima nell'host e quindi nel dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati.

## Usare l'interfaccia utente Web locale per applicare un aggiornamento o hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete.

#### Per installare l'aggiornamento o l'hotfix

1. Nell'interfaccia utente Web locale, andare a **Manutenzione** > **Aggiornamento software**.

2. In **Percorso file di aggiornamento**, immettere il nome del file per l'aggiornamento o l'hotfix. È possibile anche cercare il file di installazione dell'aggiornamento o dell'hotfix, se posizionato in una condivisione di rete. Si avvia così l'aggiornamento. Terminato l'aggiornamento, si riceve una notifica.

    ![aggiornamento dispositivo](./media/storsimple-ova-update/image43.png)

## Passaggi successivi

Scoprire di più su come [amministrazione StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0413_2016-->