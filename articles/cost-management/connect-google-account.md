---
title: Connettere un account di Google Cloud Platform a Cloudyn in Azure | Microsoft Docs
description: Connettere un account di Google Cloud Platform per visualizzare i dati relativi ai costi e all'uso nei report di Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 45d5cee92ce75c2e13f602d1ea6540698ea8abf3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078362"
---
# <a name="connect-a-google-cloud-platform-account"></a>Connettere un account di Google Cloud Platform

È possibile connettere un account Google Cloud Platform esistente a Cloudyn. Dopo aver connesso l'account a Cloudyn, i costi e i dati di utilizzo sono disponibili nei report di Cloudyn. Questo articolo contiene informazioni utili per configurare l'account di Google e connetterlo a Cloudyn.

> [!NOTE]
> Google ha modificato la sicurezza degli account, impedendo la creazione di nuove connessioni tra Cloudyn e Google. Cloudyn continua a raccogliere i dati di Google per gli utenti con Cloudyn già connesso a Google. Attualmente non è tuttavia possibile aggiungere nuovi account di Google a Cloudyn. Il team di Cloudyn non è in grado di indicare quando riprenderà il supporto per l'aggiunta di nuovi account di Google a Cloudyn. Questa nota verrà rimossa al ripristino del supporto.

## <a name="collect-project-information"></a>Raccogliere le informazioni sul progetto

È necessario prima di tutto raccogliere le informazioni sul progetto.

1. Accedere alla console di Google Cloud Platform all'indirizzo [https://console.cloud.google.com](https://console.cloud.google.com).
2. Rivedere le informazioni sul progetto da caricare in Cloudyn e prendere nota dei valori dei campi **Nome progetto** e **ID progetto**. Tenere a portata di mano queste informazioni per i passaggi successivi.  
    ![Nome del progetto e ID del progetto visualizzati nella console di Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se la fatturazione non è abilitata e collegata al progetto, creare un account di fatturazione. Per altre informazioni, vedere [Create a new billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account) (Creare un nuovo account di fatturazione).

## <a name="enable-storage-bucket-billing-export"></a>Abilitare l'esportazione dei dati di fatturazione nel bucket di archiviazione

Cloudyn recupera i dati di fatturazione di Google da un bucket di archiviazione. Tenere a portata di mano i valori dei campi **Nome bucket** e **Prefisso report**, che saranno utili più avanti durante la registrazione di Cloudyn.

L'uso di Google Cloud Storage per archiviare i report sull'utilizzo comporta costi minimi. Per altre informazioni, vedere [Prezzi di Cloud Storage](https://cloud.google.com/storage/pricing).

1. Se non si è abilitata l'esportazione dei dati di fatturazione in un file, seguire le istruzioni in [Come abilitare l'esportazione della fatturazione in un file](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Per l'esportazione dei dati di fatturazione è possibile usare il formato JSON o CSV.
2. In alternativa, nella console di Google Cloud Platform, passare a **Billing** > **Billing export** (Fatturazione - Esportazione fatturazione). Prendere nota dei valori dei campi **Bucket name** (Nome bucket) e **Report prefix** (Prefisso report).  
    ![Informazioni di esportazione fatturazione visualizzate nella pagina di esportazione della fatturazione](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Abilitare le API di Google Cloud Platform

Per raccogliere le informazioni sull'utilizzo e sugli asset, Cloudyn richiede che siano abilitate le seguenti API di Google Cloud Platform:

- BigQuery
- Google Cloud SQL
- Google Cloud Datastore
- Google Cloud Storage
- Google Cloud Storage JSON
- Google Compute Engine

### <a name="enable-or-verify-apis"></a>Abilitare o verificare le API

1. Nella console di Google Cloud Platform, selezionare il progetto che si vuole registrare con Cloudyn.
2. Passare a **APIs & Services** > **Library** (API e servizi - Libreria).
3. Usare la funzione di ricerca per trovare ogni API elencata in precedenza.
4. Per ogni API verificare che sia visualizzato lo stato **API enabled** (API abilitata). In caso contrario, fare clic su **ENABLE** (ABILITA).

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Aggiungere un account di Google Cloud a Cloudyn

1. Aprire il portale di Cloudyn dal portale di Azure oppure passare a [https://azure.cloudyn.com](https://azure.cloudyn.com/) ed eseguire l'accesso.
2. Fare clic su **Settings** (Impostazioni) (simbolo con ruota dentata) e quindi selezionare **Cloud Accounts** (Account cloud).
3. In **Accounts Management** (Gestione account) selezionare la scheda **Google Accounts** (Account Google) e quindi fare clic su **Add new +** (Aggiungi nuovo +).
4. In **Google Account Name** (Nome account Google) immettere l'indirizzo di posta elettronica per l'account di fatturazione e quindi fare clic su **Next** (Avanti).
5. Nella finestra di dialogo di autenticazione di Google selezionare o immettere un account di Google e quindi scegliere **ALLOW** (CONSENTI) per concedere l'accesso di cloudyn.com all'account.
6. Aggiungere le informazioni sul progetto della richiesta annotate nei passaggi precedenti, ovvero l'**ID** e il **nome del progetto**, il nome del bucket di **fatturazione** e il prefisso del **file dei dati di fatturazione**, quindi fare clic su **Save** (Salva).  
    ![Aggiunta di un progetto Google all'account Cloudyn](./media/connect-google-account/add-project.png)

L'account di Google verrà visualizzato nell'elenco degli account e dovrebbe risultare **Authenticated** (Autenticato). Al di sotto dell'account dovrebbero essere visualizzati il nome e l'ID del progetto Google, contrassegnati da un segno di spunta verde. Lo stato dell'account dovrebbe essere **Completed** (Completato).

Entro alcune ore, nei report di Cloudyn saranno visibili le informazioni relative a costi e uso di Google.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Cloudyn, passare all'esercitazione [Esaminare uso e costi](./tutorial-review-usage.md) per Cloudyn.
