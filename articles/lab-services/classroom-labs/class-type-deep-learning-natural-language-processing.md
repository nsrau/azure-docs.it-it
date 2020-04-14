---
title: Configurare un lab incentrato sul deep learning con Azure Lab Services. Documenti Microsoft
description: Informazioni su come configurare un lab per insegnare gli script della shell su Linux.Learn how to set up a lab to teach shell scripting on Linux.
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257760"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services
Questo articolo illustra come configurare un lab incentrato sul deep learning nell'elaborazione del linguaggio naturale (NLP) usando Azure Lab Services.This article shows you how to set up a lab focused on deep learning in natural language processing (NLP) using Azure Lab Services. L'elaborazione del linguaggio naturale è una forma di intelligenza artificiale che dota i computer di strumenti di traduzione, riconoscimento vocale e altre funzionalità di comprensione del linguaggio.  

Gli studenti che frequentano una classe di elaborazione del linguaggio naturale lavorano su una macchina virtuale Linux per apprendere come applicare gli algoritmi di rete neurale al fine di sviluppare modelli di Deep Learning usati per l'analisi del linguaggio umano scritto. 

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver creato una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services o usare un account lab esistente. Per la creazione di un nuovo account lab, vedere l'esercitazione seguente: [Esercitazione sull'installazione di un account lab](tutorial-setup-lab-account.md).
 
Dopo aver creato l'account lab, abilitare le impostazioni seguenti nell'account lab: 

| Impostazione dell'account lab | Istruzioni |
| ----------- | ------------ |  
| Immagini del Marketplace | Abilitare l'immagine Data Science Virtual Machine for Linux (Ubuntu) per l'uso all'interno dell'account lab.  Per istruzioni, vedere l'articolo seguente: Specificare le immagini del [marketplace disponibili per i creatori di lab.](specify-marketplace-images.md) | 

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo lab e applicare le impostazioni seguenti:Follow this tutorial to create a new lab and apply the following settings:

| Impostazioni lab | Valore/istruzioni | 
| ------------ | ------------------ |
| Dimensioni della macchina virtuale (VM)Virtual machine (VM) size | GPU piccola (Compute). Questa dimensione è più adatta per applicazioni ad alta intensità di elaborazione e di rete come intelligenza artificiale e Deep Learning. |
| Immagine della macchina virtualeVM image | [Data Science Virtual Machine per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Questa immagine fornisce framework e strumenti di deep learning per l'apprendimento automatico e l'analisi scientifica dei dati. Per visualizzare l'elenco completo degli strumenti installati in questa immagine, vedere il seguente articolo: [Che cosa è incluso in DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Abilitare la connessione desktop remoto | Abilita. <p>L'abilitazione di questa impostazione consentirà a insegnanti e studenti di connettersi alle proprie macchine virtuali (VM) tramite Desktop remoto (RDP).</p><p>**Importante**: RDP è già installato e configurato nell'immagine Data Science Virtual Machine for Linux. Di conseguenza, gli insegnanti/studenti possono connettersi alle macchine virtuali tramite RDP senza ulteriori passaggi. Inoltre, se è necessario connettersi al desktop grafico, in questa immagine è già installato [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) nella macchina virtuale. Gli studenti devono installare il client X2Go nei computer locali e devono utilizzare il client per la connessione. Per ulteriori informazioni, vedere le seguenti guide: <ul><li>[Come accedere alla macchina virtuale per l'analisi scientifica dei dati per Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Connettersi alla macchina virtuale modello per installare i pacchetti RDP e GUIConnect to the template VM to install RDP and GUI packages](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

L'immagine Data Science Virtual Machine for Linux fornisce i framework e gli strumenti di deep learning necessari per questo tipo di classe. Di conseguenza, dopo la creazione della macchina modello, non è necessario personalizzarlo ulteriormente. Può essere pubblicato per gli studenti di utilizzare. Selezionare il pulsante **Pubblica** nella pagina del modello per pubblicare il modello nel lab.  

## <a name="cost"></a>Costi
Se si desidera stimare il costo di questo lab, è possibile utilizzare l'esempio seguente: 

Per una classe di 25 studenti con 20 ore di tempo programmato e 10 ore di quota per compiti a casa o compiti, il prezzo per il laboratorio sarebbe - 25 studenti - (20 x 10) ore - 139 unità di laboratorio - 0,01 USD all'ora - 1042,5 USD

Ulteriori ulteriori dettagli sui prezzi, vedere Prezzi di [Servizi di laboratorio di Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni
Questo articolo illustra i passaggi per creare un lab per la classe di elaborazione del linguaggio naturale. È possibile utilizzare una configurazione simile per altre classi di deep learning.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:Next steps are common to setting up any lab:

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users). 

