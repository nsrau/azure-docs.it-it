---
title: Ambiente desktop virtuale Windows-Azure
description: Elementi di base di un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 23bf9be8e3e5f1c52546faa9ed5171c140eba59a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676632"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente di Desktop virtuale Windows

Desktop virtuale di Windows è un servizio che consente agli utenti di accedere in modo semplice e sicuro ai propri desktop e RemoteApp virtualizzati. In questo argomento viene illustrata la struttura generale dell'ambiente desktop virtuale di Windows.

## <a name="tenants"></a>tenant

Il tenant desktop virtuale di Windows è l'interfaccia principale per la gestione dell'ambiente desktop virtuale di Windows. Ogni tenant di desktop virtuale di Windows deve essere associato al Azure Active Directory contenente gli utenti che accederanno all'ambiente. Dal tenant desktop virtuale di Windows, è possibile iniziare a creare pool host per eseguire i carichi di lavoro degli utenti.

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

- Un utente non può essere assegnato sia a un gruppo di app desktop che a un gruppo di app RemoteApp nello stesso pool host.
- Un utente può essere assegnato a più gruppi di app nello stesso pool host e il relativo feed sarà un accumulo di entrambi i gruppi di app.

## <a name="tenant-groups"></a>Gruppi tenant

Nel desktop virtuale di Windows, il tenant di desktop virtuale di Windows è il punto in cui si verifica la maggior parte delle configurazioni e della configurazione. Il tenant desktop virtuale di Windows contiene i pool host, i gruppi di app e le assegnazioni utente del gruppo di app. Tuttavia, in alcuni casi è necessario gestire contemporaneamente più tenant di desktop virtuali Windows, in particolare se si è un provider di servizi cloud (CSP) o un partner di hosting. In questi casi, è possibile utilizzare un gruppo di tenant desktop virtuale di Windows personalizzato per collocare ogni tenant del desktop virtuale di Windows dei clienti e gestire centralmente l'accesso. Tuttavia, se si gestisce solo un tenant di desktop virtuale Windows singolo, il concetto di gruppo di tenant non si applica ed è possibile continuare a usare e gestire il tenant esistente nel gruppo tenant predefinito.

## <a name="end-users"></a>Utenti finali

Dopo aver assegnato gli utenti ai gruppi di app, possono connettersi a una distribuzione di desktop virtuali Windows con qualsiasi client desktop virtuale di Windows.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'accesso delegato e su come assegnare i ruoli agli utenti all' [accesso delegato nel desktop virtuale di Windows](delegated-access-virtual-desktop.md).

Per informazioni su come configurare il tenant di desktop virtuale Windows, vedere [creare un tenant in un desktop virtuale di Windows](tenant-setup-azure-active-directory.md).

Per informazioni su come connettersi a desktop virtuale di Windows, vedere uno degli articoli seguenti:

- [Connettersi da Windows 10 o Windows 7](connect-windows-7-and-10.md)
- [Connettersi da un Web browser](connect-web.md)
