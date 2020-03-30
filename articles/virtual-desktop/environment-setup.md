---
title: Ambiente Desktop virtuale Windows - Azure
description: Elementi di base di un ambiente Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127910"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente di Desktop virtuale Windows

Windows Virtual Desktop è un servizio che offre agli utenti un accesso facile e sicuro ai loro desktop virtualizzati e RemoteApps. Questo argomento illustra la struttura generale dell'ambiente Desktop virtuale di Windows.

## <a name="tenants"></a>Tenant

Il tenant di Windows Virtual Desktop è l'interfaccia principale per la gestione dell'ambiente Windows Virtual Desktop. Ogni tenant di Windows Virtual Desktop deve essere associato ad Azure Active Directory contenente gli utenti che accederanno all'ambiente. Dal tenant di Windows Virtual Desktop è possibile iniziare a creare pool host per eseguire i carichi di lavoro degli utenti.

## <a name="host-pools"></a>Pool host

Un pool host è una raccolta di macchine virtuali di Azure che eseguono la registrazione in Desktop virtuale di Windows come host di sessione quando si esegue l'agente desktop virtuale di Windows.A host pool is a collection of Azure virtual machines that register to Windows Virtual Desktop as session hosts when you run the Windows Virtual Desktop agent. Tutte le macchine virtuali dell'host di sessione in un pool host devono proavere dalla stessa immagine per un'esperienza utente coerente.

Un pool host può essere di due tipi:

- Personale, in cui ogni host di sessione è assegnato a singoli utenti.
- In pool, in cui gli host di sessione possono accettare connessioni da qualsiasi utente autorizzato a un gruppo di app all'interno del pool host.

È possibile impostare proprietà aggiuntive nel pool host per modificarne il comportamento di bilanciamento del carico, il numero di sessioni che ogni host di sessione può eseguire e le operazioni che l'utente può eseguire per gli host di sessione nel pool host dopo aver eseguito l'accesso alle sessioni di Windows Virtual Desktop. Puoi controllare le risorse pubblicate agli utenti tramite i gruppi di app.

## <a name="app-groups"></a>Gruppi di app

Un gruppo di app è un raggruppamento logico di applicazioni installate negli host di sessione nel pool host. Un gruppo di app può essere di due tipi:

- RemoteApp, dove gli utenti accedono alle RemoteApp selezionate e pubblicate singolarmente nel gruppo di app
- Desktop, dove gli utenti accedono al desktop completo

Per impostazione predefinita, un gruppo di app desktop (denominato "Gruppo di applicazioni desktop") viene creato automaticamente ogni volta che si crea un pool host. È possibile rimuovere questo gruppo di app in qualsiasi momento. Tuttavia, non è possibile creare un altro gruppo di app desktop nel pool host mentre esiste un gruppo di app desktop. Per pubblicare RemoteApps, è necessario creare un gruppo di app RemoteApp.To publish RemoteApps, you must create a RemoteApp app group. È possibile creare più gruppi di app RemoteApp per supportare scenari di lavoro diversi. Gruppi di app RemoteApp diversi possono contenere anche RemoteApp sovrapposte.

Per pubblicare risorse per gli utenti, è necessario assegnarle ai gruppi di app. Quando assegni gli utenti ai gruppi di app, considera quanto segue:

- Un utente non può essere assegnato a un gruppo di app desktop e a un gruppo di app RemoteApp nello stesso pool host.
- Un utente può essere assegnato a più gruppi di app all'interno dello stesso pool host e il feed sarà un accumulo di entrambi i gruppi di app.

## <a name="tenant-groups"></a>Gruppi di tenant

In Windows Virtual Desktop, il tenant di Windows Virtual Desktop è dove si verifica la maggior parte dell'installazione e della configurazione. Il tenant di Windows Virtual Desktop contiene i pool host, i gruppi di app e le assegnazioni degli utenti dei gruppi di app. Tuttavia, in alcune situazioni potrebbe essere necessario gestire più tenant di Windows Virtual Desktop contemporaneamente, in particolare se si è un provider di servizi cloud (CSP) o un partner di hosting. In queste situazioni, è possibile utilizzare un gruppo di tenant desktop virtuale di Windows personalizzato per posizionare ogni tenant di Windows Virtual Desktop dei clienti e gestire centralmente l'accesso. Tuttavia, se si gestisce solo un singolo tenant di Windows Virtual Desktop, il concetto di gruppo tenant non si applica ed è possibile continuare a gestire e gestire il tenant presente nel gruppo di tenant predefinito.

## <a name="end-users"></a>Utenti finali

Dopo aver assegnato gli utenti ai gruppi di app, questi possono connettersi a una distribuzione di Windows Virtual Desktop con uno qualsiasi dei client Windows Virtual Desktop.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'accesso delegato e su come assegnare ruoli agli utenti, vedere [Accesso delegato in Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Per informazioni su come configurare il tenant di Windows Virtual Desktop, vedere [Creare un tenant in Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Per informazioni su come connettersi a Windows Virtual Desktop, vedere uno degli articoli seguenti:

- [Connettersi da Windows 10 o Windows 7](connect-windows-7-and-10.md)
- [Connettersi da un Web browser](connect-web.md)
