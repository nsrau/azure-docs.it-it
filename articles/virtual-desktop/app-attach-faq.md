---
title: Domande frequenti sulla connessione di app desktop virtuali Windows MSIX-Azure
description: Domande frequenti sulla connessione app MSIX per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73fb9bf436c043e903977fafbb5a502e2edc5488
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518686"
---
# <a name="msix-app-attach-faq"></a>Domande frequenti sull'aggiunta di app MSIX

Questo articolo risponde alle domande frequenti sulla connessione app MSIX per desktop virtuale di Windows.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Qual è la differenza tra MSIX e la connessione app MSIX?

MSIX è un formato di creazione di pacchetti per le app, mentre MSIX app alleghi è la funzionalità che offre pacchetti MSIX per la distribuzione.

## <a name="does-msix-app-attach-use-fslogix"></a>La connessione all'app MSIX USA FSLogix?

La connessione all'app MSIX non usa FSLogix. Tuttavia, MSIX app alleghi e FSLogix sono progettati per collaborare per offrire un'esperienza utente uniforme.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>È possibile usare la connessione all'app MSIX all'esterno del desktop virtuale di Windows?

Le API per la connessione dell'app Power MSIX sono disponibili per Windows 10 Enterprise. Queste API possono essere usate all'esterno del desktop virtuale di Windows. Tuttavia, non esiste alcun piano di gestione per la connessione all'app MSIX all'esterno del desktop virtuale di Windows.

## <a name="how-do-i-get-an-msix-package"></a>Ricerca per categorie ottenere un pacchetto MSIX?

Il fornitore del software fornirà un pacchetto MSIX. È anche possibile convertire i pacchetti non MSIX in MSIX. Per altre informazioni [, vedere come spostare i programmi di installazione esistenti in MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Quali sistemi operativi supportano la connessione all'app MSIX?

Windows 10 Enterprise e Windows 10 Enterprise multisessione, versione 2004 o successiva.

## <a name="is-msix-app-attach-currently-generally-available"></a>La connessione all'app MSIX è attualmente disponibile a livello generale?

La connessione all'app MSIX fa parte di Windows 10 Enterprise e Windows 10 Enterprise Multisession, versione 2004 o successiva. Entrambi i sistemi operativi sono attualmente disponibili a livello generale. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>È possibile usare la connessione all'app MSIX all'esterno del desktop virtuale di Windows?

Le API di associazione delle app MSIX e MSIX fanno parte di Windows 10 Enterprise e Windows 10 Enterprise multisessione, versione 2004 e successive. Al momento non è disponibile il software di gestione per la connessione all'app MSIX all'esterno del desktop virtuale di Windows.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>È possibile eseguire due versioni della stessa applicazione nello stesso momento?

Per l'esecuzione simultanea di due versioni delle stesse applicazioni MSIX, la famiglia di pacchetti MSIX definita nel file di appxmanifest.xml deve essere diversa per ogni app.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>È necessario disabilitare l'aggiornamento automatico quando si usa la connessione all'app MSIX?

Sì. La connessione all'app MSIX non supporta l'aggiornamento automatico per le applicazioni MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Come funzionano le autorizzazioni con la connessione all'app MSIX?

Tutte le macchine virtuali in un pool host che usa l'associazione dell'app MSIX devono disporre delle autorizzazioni di lettura per la condivisione file in cui sono archiviate le immagini MSIX. Se usa anche File di Azure, sarà necessario concedere sia il controllo degli accessi in base al ruolo (RBAC) che le nuove autorizzazioni di file System (NTFS).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>È possibile usare la connessione app MSIX per HTTP o HTTPs?

Tutte le macchine virtuali che fanno parte di un pool host che usa la connessione app MSIX devono disporre delle autorizzazioni di lettura per la condivisione file in cui sono archiviate le immagini MSIX. Se File di Azure viene utilizzato sia le autorizzazioni RBAC che quelle NTFS devono essere concesse.

## <a name="can-i-restage-the-same-msix-application"></a>È possibile basare la stessa applicazione MSIX?

Sì. È possibile fare in modo che le applicazioni di cui è già stata effettuato il pre-installazione non causino errori.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>La connessione dell'app MSIX supporta i certificati autofirmati?

L'uso della connessione app MSIX su HTTP o HTTPs non è al momento supportato.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla connessione all'app MSIX, vedere la [Panoramica](what-is-app-attach.md) e il [Glossario](app-attach-glossary.md). In caso contrario, iniziare a [impostare la connessione app](app-attach.md).
