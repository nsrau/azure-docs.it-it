---
title: Caricare un&quot;immagine personalizzata per Azure RemoteApp | Documentazione Microsoft
description: Informazioni su come caricare un&quot;immagine personalizzata per RemoteApp di Azure
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: 299e0510-1a6b-4fdf-914a-3631b061a360
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: ericor
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b93861bc6277aaca5954bf9f4c338fed22a0c3fc
ms.lasthandoff: 04/27/2017


---
# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Caricare un'immagine personalizzata per RemoteApp di Azure
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Dopo aver creato o aggiornato con le modifiche l'immagine modello personalizzata, è necessario caricare l'immagine nella raccolta immagini di Azure RemoteApp. Usare i passaggi seguenti.

## <a name="before-you-start"></a>Prima di iniziare
1. Verificare che l'immagine personalizzata soddisfi i [requisiti dell'immagine](remoteapp-imagereqs.md) e i [requisiti dell'applicazione](remoteapp-appreqs.md).
2. Installare il [modulo Azure PowerShell](/powershell/azure/overview).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Istruzioni dettagliate su come caricare un'immagine personalizzata
1. Aprire il portale di gestione di Azure e passare alla pagina RemoteApp.
2. Nella scheda **Immagini modello** fare clic su **Carica** in fondo alla pagina.
3. Immettere un nome descrittivo per l'immagine e specificare il percorso dell'account di archiviazione. Verificare che il percorso sia uguale a quello della raccolta RemoteApp oppure che sia il percorso dove verrà creata una nuova raccolta.
4. Quando richiesto, scaricare lo script nel PC locale.
5. Copiare negli Appunti i parametri del comando contenuti nella casella di testo.
6. Aprire una finestra di Windows PowerShell con privilegi elevati.
7. Dalla finestra di Windows PowerShell con privilegi elevati passare alla directory in cui è stato scaricato lo script.
8. Incollare il comando copiato e premere **Invio**.
   
   Viene avviato il processo di caricamento, la cui durata dipende da diversi fattori, ad esempio la velocità di rete e le dimensioni dell'immagine
9. Se il caricamento non riesce a causa di interruzioni di rete o problemi simili, è possibile riprendere il processo di caricamento avviato in qualsiasi momento. Per riprendere il caricamento, eseguire di nuovo lo script usando la stessa riga di comando.

> [!WARNING]
> Non modificare mai lo script di caricamento. Sono stati implementati controlli specifici che assicurano che l'immagine soddisfi i requisiti per l'immagine e per l'applicazione.
> 
> 

## <a name="common-problems"></a>Problemi frequenti
* Assicurarsi di usare Windows PowerShell, e non Azure PowerShell. È necessario installare il modulo Azure PowerShell perché alcuni moduli sono necessari durante il processo di caricamento.
* Non modificare in nessun caso lo script. Le convalide sono state applicate per semplificare l'utilizzo da parte degli utenti.
* Se il file VHD si blocca durante il caricamento, copiare il file o spostarlo in un nuovo percorso e riprovare il caricamento. Il caricamento potrebbe essere ostacolato da qualche processo di Windows in esecuzione.  


