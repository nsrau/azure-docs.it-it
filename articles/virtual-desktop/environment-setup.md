---
title: Ambiente desktop virtuale Windows-Azure
description: Elementi di base di un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad535dd18b89cbe2fceab90f73789180ad332b57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612368"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente di Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/environment-setup-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Desktop virtuale di Windows è un servizio che consente agli utenti di accedere in modo semplice e sicuro ai propri desktop e RemoteApp virtualizzati. In questo argomento viene illustrata la struttura generale dell'ambiente desktop virtuale di Windows.

## <a name="host-pools"></a>Pool host

Un pool host è una raccolta di macchine virtuali di Azure che si registrano in desktop virtuali Windows come host di sessione quando si esegue l'agente desktop virtuale di Windows. Tutte le macchine virtuali host sessione in un pool host devono essere originate dalla stessa immagine per un'esperienza utente coerente.

Un pool di host può essere di uno dei due tipi seguenti:

- Personale, in cui ogni host sessione viene assegnato a singoli utenti.
- In pool, in cui gli host di sessione possono accettare connessioni da qualsiasi utente autorizzato a un gruppo di app all'interno del pool host.

È possibile impostare proprietà aggiuntive nel pool host per modificare il comportamento di bilanciamento del carico, il numero di sessioni che possono essere eseguite da ogni host della sessione e le operazioni che l'utente può eseguire per gli host di sessione nel pool host durante l'accesso alle sessioni di desktop virtuale di Windows. Si controllano le risorse pubblicate dagli utenti tramite i gruppi di app.

## <a name="app-groups"></a>Gruppi di app

Un gruppo di app è un raggruppamento logico di applicazioni installate negli host di sessione nel pool di host. Un gruppo di app può essere di uno dei due tipi seguenti:

- RemoteApp, in cui gli utenti accedono ai RemoteApp selezionati singolarmente e pubblicano nel gruppo di app
- Desktop, in cui gli utenti accedono al desktop completo

Per impostazione predefinita, ogni volta che si crea un pool host, viene creato automaticamente un gruppo di app desktop (denominato "gruppo di applicazioni desktop"). È possibile rimuovere questo gruppo di app in qualsiasi momento. Tuttavia, non è possibile creare un altro gruppo di app desktop nel pool host mentre è presente un gruppo di applicazioni desktop. Per pubblicare RemoteApp, è necessario creare un gruppo di app RemoteApp. È possibile creare più gruppi di app RemoteApp per adattarsi a diversi scenari di lavoro. I diversi gruppi di app RemoteApp possono anche contenere RemoteApp sovrapposti.

Per pubblicare le risorse per gli utenti, è necessario assegnarle ai gruppi di app. Quando si assegnano utenti a gruppi di app, tenere presente quanto segue:

- Un utente può essere assegnato sia a un gruppo di app desktop che a un gruppo di app RemoteApp nello stesso pool host. Tuttavia, gli utenti possono avviare un solo tipo di gruppo di app per sessione. Gli utenti non possono avviare entrambi i tipi di gruppi di app nello stesso momento in un'unica sessione.
- Un utente può essere assegnato a più gruppi di app nello stesso pool host e il relativo feed sarà un accumulo di entrambi i gruppi di app.

## <a name="workspaces"></a>Aree di lavoro

Un'area di lavoro è un raggruppamento logico di gruppi di applicazioni nel desktop virtuale di Windows. Ogni gruppo di applicazioni desktop virtuali Windows deve essere associato a un'area di lavoro per consentire agli utenti di visualizzare le app Remote e i desktop pubblicati.  

## <a name="end-users"></a>Utenti finali

Dopo aver assegnato gli utenti ai gruppi di app, possono connettersi a una distribuzione di desktop virtuali Windows con qualsiasi client desktop virtuale di Windows.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'accesso delegato e su come assegnare i ruoli agli utenti all' [accesso delegato nel desktop virtuale di Windows](delegated-access-virtual-desktop.md).

Per informazioni su come configurare il pool di host per desktop virtuali Windows, vedere [creare un pool host con il portale di Azure](create-host-pools-azure-marketplace.md).

Per informazioni su come connettersi a desktop virtuale di Windows, vedere uno degli articoli seguenti:

- [Connessione con Windows 10 o Windows 7](connect-windows-7-and-10.md)
- [Connettersi con un Web browser](connect-web.md)
- [Connettersi con il client Android](connect-android.md)
- [Connettersi con il client macOS](connect-macos.md)
- [Connettersi con il client iOS](connect-ios.md)