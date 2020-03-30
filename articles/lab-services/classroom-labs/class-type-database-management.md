---
title: Impostare un lab per insegnare la gestione dei database per i database relazionali . Documenti Microsoft
description: Informazioni su come configurare un lab per illustrare la gestione dei database relazionali.
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
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469919"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Impostare un lab per insegnare la gestione dei database per i database relazionali

Questo articolo descrive come configurare un lab per una classe di gestione di database di base in Azure Lab Services.This article describes how to set up a lab for a basic databases management class in Azure Lab Services. I concetti relativi ai database sono uno dei corsi introduttivi offerti nella maggior parte delle facoltà di scienze informatiche. SQL (Structured Query Language) è uno standard internazionale. SQL è il linguaggio standard per la gestione delle relazioni del database, tra cui l'aggiunta, l'accesso e la gestione del contenuto in un database.  E 'più noto per la sua elaborazione rapida, comprovata affidabilità, facilità, e la flessibilità di utilizzo.

In questo articolo verrà illustrato come configurare un modello di macchina virtuale in un lab con server di database MySQL e server SQL Server 2019.  [MySQL](https://www.mysql.com/) è un sistema di gestione di database relazionale (RDBMS) open source disponibile gratuitamente.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) è la versione più recente di Microsoft RDBMS.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo lab, sono necessari una sottoscrizione di Azure e un account lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services.Once you get an Azure subscription, you can create a new lab account in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account lab, vedere [Esercitazione sull'installazione](tutorial-setup-lab-account.md)di un account lab .  È inoltre possibile utilizzare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account lab

Abilitare le impostazioni descritte nella tabella seguente per l'account lab. Per ulteriori informazioni su come abilitare le immagini del marketplace, consultate Specificare le immagini del [Marketplace disponibili per i creatori di lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
|Immagine del Marketplace| Abilitare l'immagine 'SQL Server 2019 Standard in Windows Server 2019' per l'utilizzo all'interno dell'account lab.|

### <a name="lab-settings"></a>Impostazioni lab

Utilizza le impostazioni nella tabella seguente quando configuri un laboratorio di classe.  Per ulteriori informazioni su come creare un lab della classe, vedere [Configurare un'esercitazione](tutorial-setup-classroom-lab.md)del laboratorio in classe .

| Impostazioni lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| Media. Questa dimensione è più adatta per i database relazionali, la memorizzazione nella cache in memoria e l'analisi.|
|Immagine di macchina virtuale| SQL Server 2019 Standard in Windows Server 2019|

## <a name="template-machine-configuration"></a>Configurazione macchina modello

Per installare MySQL in Windows Server 2019, è possibile seguire i passaggi indicati in [Installare ed eseguire MySQL Community Server in una macchina virtuale](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 è preinstallato nell'immagine della macchina virtuale scelta durante la creazione del nuovo lab.

## <a name="cost-estimate"></a>Preventivo

Copriamo una possibile stima dei costi per questa classe.  Useremo una classe di 25 studenti.  Ci sono 20 ore di tempo programmato per la lezione.  Inoltre, ogni studente riceve 10 ore di quota per i compiti a casa o i compiti al di fuori dell'orario previsto.  La dimensione della macchina virtuale scelta era media, ovvero 42 unità lab.

Ecco un esempio di una possibile stima dei costi per questa classe:

25 \* studenti (20 ore programmate e 10 ore di quota) \* 0,42 USD all'ora e 315,00 USD

Ulteriori ulteriori dettagli sui prezzi, vedere Prezzi di [Servizi di laboratorio di Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni

In questo articolo sono stati illustrati i passaggi necessari per creare un lab per i concetti di base di gestione di database utilizzando sia MySQL che SQL Server. È possibile utilizzare un'impostazione simile per altre classi di database.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
