---
title: Errore durante la creazione o l'eliminazione di un database o di una tabella in Esplora dati di Azure
description: Questo articolo illustra come risolvere i problemi riscontrati durante la creazione e l'eliminazione di database e tabelle in Esplora dati di Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6ec81d6154f15d1c49428b50f0e65eed8edcedad
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986515"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Risoluzione dei problemi: Errore durante la creazione o l'eliminazione di un database o di una tabella in Esplora dati di Azure

In Esplora dati di Azure vengono regolarmente usati database e tabelle. Questo articolo illustra le procedure di risoluzione dei problemi riscontrati.

## <a name="creating-a-database"></a>Creazione di un database

1. Verificare di disporre delle autorizzazioni appropriate. Per creare un database è necessario essere un membro del ruolo *Collaboratore* oppure *Proprietario* della sottoscrizione di Azure. Se necessario rivolgersi all'amministratore della sottoscrizione per essere aggiunti al ruolo appropriato.

1. Verificare che non siano presenti errori di convalida del nome per il nome del database. Il nome deve essere alfanumerico con una lunghezza massima di 260 caratteri.

1. Verificare che il periodo di conservazione del database e i valori di memorizzazione nella cache siano compresi negli intervalli consentiti. Il periodo di conservazione deve essere compreso tra 1 e 36500 (100 giorni). Il valore della memorizzazione nella cache deve essere compreso tra 1 e il valore massimo impostato per la conservazione dei dati.

## <a name="deleting-or-renaming-a-database"></a>Eliminazione o ridenominazione di un database

Verificare di disporre delle autorizzazioni appropriate. Per eliminare o ridenominare un database è necessario essere un membro del ruolo *Collaboratore* oppure *Proprietario* della sottoscrizione di Azure. Se necessario rivolgersi all'amministratore della sottoscrizione per essere aggiunti al ruolo appropriato.

## <a name="creating-a-table"></a>Creazione di una tabella

1. Verificare di disporre delle autorizzazioni appropriate. Per creare una tabella è necessario essere un membro del ruolo *Amministratore database* o *Utente database* del database o del ruolo *Collaboratore* oppure *Proprietario* ruolo della sottoscrizione di Azure. Se necessario rivolgersi all'amministratore della sottoscrizione o del cluster per essere aggiunti al ruolo appropriato.

    Per altre informazioni sulle autorizzazioni, vedere [Gestire le autorizzazioni per i database](manage-database-permissions.md).

1. Verificare che non esista già una tabella con lo stesso nome. Nel caso in cui esista, è possibile: creare una tabella con un nome diverso; ridenominare la tabella esistente (è necessario il ruolo *Amministratore tabella*); eliminare la tabella esistente (è necessario il ruolo *Amministratore database*). Usare i comandi seguenti.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Eliminazione o ridenominazione di una tabella

Verificare di disporre delle autorizzazioni appropriate. Per eliminare o rinominare una tabella, è necessario essere un membro del ruolo *Amministratore database* oppure *Amministratore tabella* del database. Se necessario rivolgersi all'amministratore della sottoscrizione o del cluster per essere aggiunti al ruolo appropriato.

Per altre informazioni sulle autorizzazioni, vedere [Gestire le autorizzazioni per i database](manage-database-permissions.md).

## <a name="general-guidance"></a>Indicazioni generali

1. Controllare il [dashboard dell'integrità dei servizi di Azure](https://azure.microsoft.com/status/>). Individuare lo stato di Esplora dati di Azure nell'area in cui si sta cercando di lavorare con il database o la tabella.

    Se lo stato non è **Buono** (segno di spunta verde), provare di nuovo non appena lo stato migliora.

1. Per richiedere ulteriore assistenza per la risoluzione del problema, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com).

## <a name="next-steps"></a>Passaggi successivi

[Controllare l'integrità del cluster](check-cluster-health.md)