---
title: Aggiornamento della versione principale-portale di Azure-database di Azure per MySQL-server singolo
description: Questo articolo descrive come è possibile aggiornare la versione principale per database di Azure per MySQL-server singolo con portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753052"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Aggiornamento della versione principale nel server singolo database di Azure per MySQL con il portale di Azure

> [!IMPORTANT]
> L'aggiornamento della versione principale per il server singolo di database di Azure per MySQL è in anteprima pubblica.

Questo articolo descrive come aggiornare la versione principale di MySQL sul posto nel database di Azure per il singolo server MySQL.

Questa funzionalità consentirà ai clienti di eseguire aggiornamenti sul posto dei propri server MySQL 5,6 a MySQL 5,7 con un clic del pulsante senza lo spostamento dei dati o la necessità di modificare le stringhe di connessione dell'applicazione.

> [!Note]
> * L'aggiornamento della versione principale è disponibile solo per l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7<br>
> * L'aggiornamento della versione principale non è ancora supportato nel server di replica.
> * Il server non sarà disponibile durante l'operazione di aggiornamento. È pertanto consigliabile eseguire gli aggiornamenti durante la finestra di manutenzione pianificata.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [singolo server di database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Eseguire l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7

Seguire questa procedura per eseguire l'aggiornamento principale della versione per il database di Azure del server MySQL 5,6

> [!IMPORTANT]
> È consigliabile eseguire prima l'aggiornamento su una copia ripristinata del server anziché aggiornare direttamente la produzione. Vedere [come eseguire il ripristino temporizzato](howto-restore-server-portal.md#point-in-time-restore). 

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server di database di Azure per MySQL 5,6 esistente.

2. Nella pagina **Panoramica** fare clic sul pulsante **Aggiorna** sulla barra degli strumenti.

3. Nella sezione **aggiornamento** selezionare **OK** per aggiornare il server di database di Azure per MySQL 5,6 al server 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Database di Azure per MySQL-Panoramica-aggiornamento":::

4. Una notifica conferma che l'aggiornamento ha avuto esito positivo.

## <a name="frequently-asked-questions"></a>Domande frequenti

**1. quando la funzionalità di aggiornamento sarà GA perché nell'ambiente di produzione è necessario aggiornare MySQL v 5.6?**

La GA di questa funzionalità è pianificata prima del ritiro di MySQL v 5.6. Tuttavia, la funzionalità è pronta per la produzione e completamente supportata da Azure, pertanto è consigliabile eseguirla in tutta sicurezza nell'ambiente in uso. Come procedura consigliata, è consigliabile eseguire e testare prima di tutto una copia ripristinata del server in modo che sia possibile stimare il tempo di inattività durante l'aggiornamento ed eseguire il test di compatibilità delle applicazioni prima di eseguirlo nell'ambiente di produzione. Per ulteriori informazioni, vedere [come eseguire il ripristino temporizzato](howto-restore-server-portal.md#point-in-time-restore) per creare una copia temporizzata del server. 

**2. questa operazione causerà tempi di inattività del server e, in caso affermativo, per quanto tempo?**

Sì, il server non sarà disponibile durante il processo di aggiornamento, pertanto si consiglia di eseguire questa operazione durante la finestra di manutenzione pianificata. Il tempo di inattività stimato dipende dalla dimensione del database, dalle dimensioni di archiviazione di cui è stato effettuato il provisioning (IOPs con provisioning) e dal numero di tabelle nel database. Il tempo di aggiornamento è direttamente proporzionale al numero di tabelle nel server. Gli aggiornamenti dei server SKU Basic dovrebbero richiedere più tempo, perché si trova nella piattaforma di archiviazione standard. Per stimare il tempo di inattività per l'ambiente server, è consigliabile eseguire prima l'aggiornamento sulla copia ripristinata del server.  

**3. si noti che non è ancora supportata nel server di replica. Che cosa significa concreto?**

Attualmente, l'aggiornamento della versione principale non è supportato per il server di replica, pertanto non è consigliabile eseguirlo per i server che interessano la replica, ovvero il server di origine o di replica. Se si desidera testare l'aggiornamento dei server che coinvolgono la replica prima di aggiungere il supporto della replica per la funzionalità di aggiornamento, è consigliabile seguire questa procedura:

1. Durante la manutenzione pianificata, [arrestare la replica ed eliminare il server di replica](howto-read-replicas-portal.md) dopo aver acquisito il nome e tutte le informazioni di configurazione (impostazioni del firewall, configurazione dei parametri del server se è diversa da quella del server di origine).
2. Eseguire l'aggiornamento del server di origine.
3. Eseguire il provisioning di un nuovo server di replica di lettura con le stesse impostazioni di configurazione e nome acquisite nel passaggio 1. Il nuovo server di replica sarà automaticamente in v 5.7 dopo l'aggiornamento del server di origine a v 5.7.

**4. cosa accade se non si sceglie di aggiornare il server MySQL v 5.6 prima del 5 febbraio 2021?**

È comunque possibile continuare a eseguire il server MySQL v 5.6 come prima. Azure **non** eseguirà mai l'aggiornamento forzato sul server. Tuttavia, verranno applicate le restrizioni documentate nei [criteri di controllo delle versioni del database di Azure per MySQL](concepts-version-policy.md) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [criteri di controllo delle versioni di database di Azure per MySQL](concepts-version-policy.md).
