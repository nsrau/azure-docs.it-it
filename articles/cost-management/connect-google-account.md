---
title: Connettere un account di Google Cloud Platform a Gestione costi di Azure | Microsoft Docs
description: Connettere un account di Google Cloud Platform per visualizzare i dati relativi ai costi e all'utilizzo nei report di Gestione costi.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 81a38f471ee1f2f8064a956eca121fd0e6feb235
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Connettere un account di Google Cloud Platform

È possibile connettere un account esistente di Google Cloud Platform a Gestione costi di Azure. Dopo la connessione dell'account a Gestione costi, i dati relativi ai costi e all'utilizzo sono disponibili nei report di Gestione costi. Questo articolo contiene informazioni utili per configurare l'account di Google e connetterlo a Gestione costi.

## <a name="collect-project-information"></a>Raccogliere le informazioni sul progetto

È necessario prima di tutto raccogliere le informazioni sul progetto.

1. Accedere alla console di Google Cloud Platform all'indirizzo [https://console.cloud.google.com](https://console.cloud.google.com).
2. Rivedere le informazioni sul progetto da caricare in Gestione costi e prendere nota dei valori dei campi **Project name** (Nome progetto) e **Project ID** (ID progetto). Tenere a portata di mano queste informazioni per i passaggi successivi.  
    ![Console di Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se la fatturazione non è abilitata e collegata al progetto, creare un account di fatturazione. Per altre informazioni, vedere [Create a new billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account) (Creare un nuovo account di fatturazione).

## <a name="enable-storage-bucket-billing-export"></a>Abilitare l'esportazione dei dati di fatturazione nel bucket di archiviazione

Gestione costi recupera i dati di fatturazione di Google da un bucket di archiviazione. Tenere a portata di mano i valori dei campi **Bucket name** (Nome bucket) e **Report prefix** (Prefisso report) che saranno utili più avanti durante la registrazione di Gestione costi.

L'uso di Google Cloud Storage per archiviare i report sull'utilizzo comporta costi minimi. Per altre informazioni, vedere [Prezzi di Cloud Storage](https://cloud.google.com/storage/pricing).

1. Se non si è abilitata l'esportazione dei dati di fatturazione in un file, seguire le istruzioni in [Come abilitare l'esportazione della fatturazione in un file](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Per l'esportazione dei dati di fatturazione è possibile usare il formato JSON o CSV.
2. In alternativa, nella console di Google Cloud Platform, passare a **Billing** > **Billing export** (Fatturazione - Esportazione fatturazione). Prendere nota dei valori dei campi **Bucket name** (Nome bucket) e **Report prefix** (Prefisso report).  
    ![Esportazione dei dati di fatturazione](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Abilitare le API di Google Cloud Platform

Per raccogliere le informazioni sull'utilizzo e sugli asset, Gestione costi richiede che siano abilitate le API di Google Cloud Platform seguenti:

- BigQuery
- Google Cloud SQL
- Google Cloud Datastore
- Google Cloud Storage
- Google Cloud Storage JSON
- Google Compute Engine

### <a name="enable-or-verify-apis"></a>Abilitare o verificare le API

1. Nella console di Google Cloud Platform selezionare il progetto che si vuole registrare con Gestione costi.
2. Passare a **APIs & Services** > **Library** (API e servizi - Libreria).
3. Usare la funzione di ricerca per trovare ogni API elencata in precedenza.
4. Per ogni API verificare che sia visualizzato lo stato **API enabled** (API abilitata). In caso contrario, fare clic su **ENABLE** (ABILITA).

## <a name="add-a-google-cloud-account-to-cost-management"></a>Aggiungere un account di Google Cloud a Gestione costi

1. Aprire il portale Cloudyn dal portale di Azure o passare a [https://azure.cloudyn.com](https://azure.cloudyn.com/) e accedere.
2. Fare clic su **Settings** (Impostazioni) (simbolo con ruota dentata) e quindi selezionare **Cloud Accounts** (Account cloud).
3. In **Accounts Management** (Gestione account) selezionare la scheda **Google Accounts** (Account Google) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. In **Google Account Name** (Nome account Google) immettere l'indirizzo di posta elettronica per l'account di fatturazione e quindi fare clic su **Next** (Avanti).
5. Nella finestra di dialogo di autenticazione di Google selezionare o immettere un account di Google e quindi scegliere **ALLOW** (CONSENTI) per concedere l'accesso di cloudyn.com all'account.
6. Aggiungere le informazioni sul progetto della richiesta annotate nei passaggi precedenti, ovvero l'**ID** e il **nome del progetto**, il nome del bucket di **fatturazione** e il prefisso del **file dei dati di fatturazione**, quindi fare clic su **Save** (Salva).  
    ![Aggiungere il progetto di Google](./media/connect-google-account/add-project.png)

L'account di Google verrà visualizzato nell'elenco degli account e dovrebbe risultare **Authenticated** (Autenticato). Al di sotto dell'account dovrebbero essere visualizzati il nome e l'ID del progetto Google, contrassegnati da un segno di spunta verde. Lo stato dell'account dovrebbe essere **Completed** (Completato).

Entro alcune ore, nei report di Gestione costi saranno visibili le informazioni relative ai costi e all'utilizzo di Google.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Gestione costi di Azure di Cloudyn, passare all'esercitazione [Esaminare l'utilizzo e i costi](./tutorial-review-usage.md) per Gestione costi.
