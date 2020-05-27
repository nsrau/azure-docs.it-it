---
title: Configurare un lab incentrato sul Deep Learning con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un lab per insegnare lo script della shell in Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 1167846c399430bd2db2eaa3114628ebb63ce639
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592322"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services
Questo articolo illustra come configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services. L'elaborazione del linguaggio naturale è una forma di intelligenza artificiale che dota i computer di strumenti di traduzione, riconoscimento vocale e altre funzionalità di comprensione del linguaggio.  

Gli studenti che frequentano una classe di elaborazione del linguaggio naturale lavorano su una macchina virtuale Linux per apprendere come applicare gli algoritmi di rete neurale al fine di sviluppare modelli di Deep Learning usati per l'analisi del linguaggio umano scritto. 

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Quando è disponibile una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services o usare un account lab esistente. Per la creazione di un nuovo account lab, vedere l'esercitazione seguente: [Esercitazione per configurare un account lab](tutorial-setup-lab-account.md).
 
Dopo aver creato l'account lab, abilitare le impostazioni seguenti nell'account lab: 

| Impostazione dell'account lab | Istruzioni |
| ----------- | ------------ |  
| Immagini del Marketplace | Abilitare l'immagine Data Science Virtual Machine per Linux (Ubuntu) per l'uso nell'account lab.  Per le istruzioni, vedere l'articolo seguente: [Specificare le immagini del Marketplace disponibili per gli autori di lab](specify-marketplace-images.md). | 

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo lab e applicare le impostazioni seguenti:

| Impostazioni del lab | Valore/istruzioni | 
| ------------ | ------------------ |
| Dimensioni della macchina virtuale | GPU small (calcolo). Queste dimensioni sono più indicate per applicazioni a elevato utilizzo di calcolo e di rete quali intelligenza artificiale e Deep Learning. |
| Immagine della macchina virtuale | [Data Science Virtual Machine per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Questa immagine fornisce framework per Deep Learning e strumenti per apprendimento automatico e data science. Per visualizzare l'elenco completo degli strumenti installati in questa immagine, vedere l'articolo seguente: [Quali funzionalità sono incluse nella DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Abilita Connessione Desktop remoto | <p>L'abilitazione di questa impostazione consentirà a docenti e studenti di connettersi alle macchine virtuali usando Desktop remoto (RDP).</p><p>**Importante**: quando si abilita questa impostazione, viene aperta solo la porta **RDP** nei computer Linux. Se il protocollo RDP è già installato e configurato nell'immagine della macchina virtuale, gli studenti e il docente possono connettersi alle macchine virtuali tramite RDP senza eseguire ulteriori operazioni. <p>Se il protocollo RDP non è installato e configurato nell'immagine della macchina virtuale, è necessario connettersi al computer Linux la prima volta usando SSH e quindi installare i pacchetti dell'interfaccia utente grafica e RDP in modo che studenti e docente in seguito possano connettersi al computer Linux tramite RDP. Per altre informazioni, vedere [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../../virtual-machines/linux/use-remote-desktop.md). Pubblicare quindi l'immagine in modo che gli studenti possano usare il protocollo RDP nelle macchine virtuali Linux.  |

L'immagine Data Science Virtual Machine per Linux fornisce framework e strumenti di Deep Learning necessari per questo tipo di classe. Di conseguenza, dopo la creazione del modello di macchina virtuale, non è necessario personalizzarla ulteriormente e può essere pubblicata e resa disponibile agli studenti. Selezionare il pulsante **Pubblica** nella pagina del modello per pubblicare il modello nel lab.  

## <a name="cost"></a>Costi
Se si vuole stimare il costo di questo lab, è possibile usare l'esempio seguente: 

Per una classe di 25 studenti con 20 ore di lezioni pianificate e 10 ore di quota per compiti o assegnazioni, il prezzo del lab sarà dato dalla formula seguente: 25 studenti * (20 + 10) ore * 139 unità lab * 0,01 USD all'ora = 1042,5 USD.

Per altre informazioni sui prezzi, vedere [Prezzi di Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni
In questo articolo è stata illustrata la procedura per creare un lab per la classe di elaborazione del linguaggio naturale. È possibile usare una configurazione simile per altre classi di Deep Learning.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Inviare i collegamenti di registrazione agli studenti tramite posta elettronica](how-to-configure-student-usage.md#send-invitations-to-users). 

