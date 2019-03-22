---
title: Ambiente di Desktop virtuale di Windows (anteprima) - Azure
description: Elementi di base di un ambiente di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: c503f665e3e2f99cd59f207c3fc4823b927eaac3
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318450"
---
# <a name="windows-virtual-desktop-environment-preview"></a>Ambiente di Desktop virtuale di Windows (anteprima)

Windows Virtual Desktop (anteprima) è un servizio che offre agli utenti un accesso facile e sicuro ai propri desktop virtualizzati e su macchine virtuali. In questo argomento illustrerà alcune informazioni la struttura generale dell'ambiente di Desktop virtuale Windows.

## <a name="tenants"></a>Tenant

Il tenant di Desktop virtuale Windows è l'interfaccia principale per la gestione dell'ambiente di Desktop virtuale Windows. È necessario associare ogni tenant di Desktop virtuale Windows con Azure Active Directory che contiene gli utenti che eseguiranno l'accesso all'ambiente. Il tenant di Desktop virtuale Windows, è possibile iniziare la creazione di pool di host per l'esecuzione di carichi di lavoro degli utenti.

## <a name="host-pools"></a>Pool di host

Un pool di host è una raccolta di macchine virtuali di Azure che registra per Windows Desktop virtuale come host della sessione quando si esegue l'agente di Windows Desktop virtuale. Tutte le macchine virtuali host sessione in un pool di host devono provenire dalla stessa immagine per un'esperienza utente coerente.

Un pool di host può essere uno dei due tipi:

- Personale, in ogni host della sessione viene assegnato a singoli utenti.
- In pool, in cui gli host della sessione possono essere accettate connessioni da qualsiasi utente autorizzato a un gruppo di app all'interno del pool di host.

È possibile impostare proprietà aggiuntive nel pool di host per modificarne il comportamento di bilanciamento del carico, il numero di sessioni può richiedere ogni host della sessione, e ciò che l'utente può eseguire agli host di sessione nel pool di host, eseguito l'accesso alle proprie sessioni di Desktop virtuale Windows. Controllare le risorse pubblicate agli utenti tramite i gruppi di app.

## <a name="app-groups"></a>Gruppi di App

Un gruppo di app è un raggruppamento logico delle applicazioni installate in host di sessione nel pool di host. Un gruppo di app può essere uno dei due tipi:

- RemoteApp, in cui gli utenti accedere il RemoteApps è singolarmente selezionare e pubblicare il gruppo di app
- Desktop, in cui gli utenti accedono al desktop completo

Per impostazione predefinita, un gruppo di app desktop (denominato "Gruppo di applicazioni Desktop") viene creato automaticamente quando si crea un pool di host. È possibile rimuovere questo gruppo di app in qualsiasi momento. Tuttavia, è possibile creare un altro gruppo di app desktop nel pool di host, mentre è presente un gruppo di app desktop. Per pubblicare su macchine virtuali, è necessario creare un gruppo di app di RemoteApp. È possibile creare più gruppi di app di RemoteApp per consentire scenari di lavoro diverso. Gruppi di app diversi RemoteApp possono inoltre contenere sovrapposte su macchine virtuali.

Per pubblicare le risorse per gli utenti, è necessario assegnarli a gruppi di app. Assegnare gli utenti ai gruppi di app, prendere in considerazione quanto segue:

- Un utente non può essere assegnato in un gruppo di app desktop e in un gruppo di app di RemoteApp nello stesso pool di host.
- Un utente può essere assegnato a più gruppi di app all'interno dello stesso pool di host e relativi feed sarà un accumulo di entrambi i gruppi di app.

## <a name="tenant-groups"></a>Gruppi del tenant

Nel Desktop virtuale Windows, il tenant di Desktop virtuale Windows è in cui viene eseguita la maggior parte del programma di installazione e configurazione. Il tenant di Windows Desktop virtuale contiene il pool di host, gruppi di app e le assegnazioni di app gruppo utente. Tuttavia, potrebbero esserci situazioni in cui è necessario gestire più tenant di Desktop virtuale Windows in una sola volta, in particolare se si è un Provider del servizio Cloud (CSP) o un partner di hosting. In queste situazioni, è possibile utilizzare un gruppo di tenant di Desktop virtuale Windows personalizzato per inserire ognuno dei tenant di Desktop virtuale Windows di clienti e gestire centralmente l'accesso. Tuttavia, se si gestiscono solo un singolo tenant di Desktop virtuale Windows, il concetto di gruppo di tenant non è applicabile ed è possibile continuare a funzionare e gestire il tenant esistente nel gruppo di tenant predefinito.

## <a name="end-users"></a>Utenti finali

Dopo aver assegnato gli utenti ai gruppi di app, è possibile connettersi a una distribuzione di Desktop virtuale Windows con uno qualsiasi dei client Windows Desktop virtuale.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come assegnare ruoli agli utenti e l'accesso delegato [delega l'accesso in Windows Desktop virtuale](delegated-access-virtual-desktop.md).

Per informazioni su come configurare il tenant di Desktop virtuale Windows, vedere [creare un tenant in Windows Desktop virtuale](tenant-setup-azure-active-directory.md).

Per informazioni su come connettersi a Desktop virtuali di Windows, vedere uno degli articoli seguenti:

- [Connettersi al client Desktop remoto in Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Connettersi al client web di Desktop virtuale Windows](connect-web.md)
