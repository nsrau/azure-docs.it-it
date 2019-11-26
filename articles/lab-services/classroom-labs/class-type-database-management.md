---
title: Configurare un Lab per insegnare la gestione dei database per i database relazionali | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare la gestione dei database relazionali.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 92152652ca264cace12d3f780ebec0f80bb2048a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233766"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Configurare un Lab per insegnare la gestione dei database per i database relazionali

Questo articolo descrive come configurare un Lab per una classe di gestione di database di base in Azure Lab Services. I concetti relativi ai database sono uno dei corsi introduttivi che hanno insegnato nella maggior parte dei reparti di informatica dell'Università. Structured Query Language (SQL) è uno standard internazionale. SQL è il linguaggio standard per la gestione dei database relazionali, tra cui l'aggiunta, l'accesso e la gestione del contenuto in un database.  È più indicato per l'elaborazione rapida, l'affidabilità comprovata, la facilità e la flessibilità di utilizzo.

Questo articolo illustra come configurare un modello di macchina virtuale in un Lab con il server di database MySQL e con SQL Server Server 2019.  [MySQL](https://www.mysql.com/) è un sistema di gestione di database relazionali Open Source disponibile gratuitamente (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) è la versione più recente di RDBMS Microsoft.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo Lab, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).  È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni su come abilitare le immagini del Marketplace, vedere [specificare le immagini del Marketplace disponibili per gli autori del Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#specify-marketplace-images-available-to-lab-creators).

| Impostazione dell'account Lab | Istruzioni |
| ------------------- | ------------ |
|Immagine del Marketplace| Abilitare l'immagine ' SQL Server 2019 standard in Windows Server 2019' per l'uso nell'account Lab.|

### <a name="lab-settings"></a>Impostazioni Lab

Usare le impostazioni nella tabella seguente quando si configura un Lab della classe.  Per altre informazioni su come creare un Lab per le aule, vedere [l'esercitazione sulla configurazione di una classe Lab](tutorial-setup-classroom-lab.md).

| Impostazioni Lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| Media. Queste dimensioni sono ideali per database relazionali, Caching in memoria e analisi.|
|Immagine della macchina virtuale| SQL Server 2019 standard in Windows Server 2019|

## <a name="template-machine-configuration"></a>Configurazione computer modello

Per installare MySQL in Windows Server 2019, è possibile seguire la procedura descritta in [installare ed eseguire MySQL Community Server in una macchina virtuale](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 è preinstallato nell'immagine di macchina virtuale selezionata durante la creazione del nuovo Lab.

## <a name="cost-estimate"></a>Stima dei costi

Verrà ora trattata una possibile stima dei costi per questa classe.  Verrà usata una classe di 25 studenti.  Sono previste 20 ore di tempo della classe pianificata.  Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato.  Le dimensioni della macchina virtuale scelte sono medie, ovvero 42 unità Lab.

Di seguito è riportato un esempio di una possibile stima dei costi per questa classe:

25 studenti \* (20 ore pianificate + 10 ore di quota) \* 0,42 USD all'ora = 315,00 USD

Per ulteriori informazioni sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusione

Questo articolo illustra i passaggi necessari per creare un Lab per i concetti di base sulla gestione dei database usando MySQL e SQL Server. È possibile utilizzare una configurazione simile per altre classi di database.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
