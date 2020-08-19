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
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556272"
---
# <a name="msix-app-attach-faq"></a>Domande frequenti sull'aggiunta di app MSIX

Questo articolo risponde alle domande frequenti sulla connessione app MSIX per desktop virtuale di Windows.

## <a name="does-msix-app-attach-use-fslogix"></a>La connessione all'app MSIX USA FSLogix?

La connessione all'app MSIX non usa FSLogix. Tuttavia, le app di alconnessione e FSLogix sono progettate per collaborare per offrire un'esperienza utente ottimale.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>È possibile usare la connessione all'app MSIX all'esterno del desktop virtuale di Windows?

Sì, la connessione app MSIX è una funzionalità inclusa in Windows 10 Enterprise e può essere usata all'esterno del desktop virtuale di Windows. Tuttavia, non esiste alcun piano di gestione per la connessione all'app MSIX all'esterno del desktop virtuale di Windows.

## <a name="how-do-i-get-an-msix-package"></a>Ricerca per categorie ottenere un pacchetto MSIX?

Il fornitore del software fornirà un pacchetto MSIX. È anche possibile convertire i pacchetti non MSIX in MSIX. Per altre informazioni [, vedere come spostare i programmi di installazione esistenti in MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Quali sistemi operativi supportano la connessione all'app MSIX?

Windows 10 Enterprise e Windows 10 Enterprise multisessione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla connessione all'app MSIX, vedere il [Glossario](app-attach-glossary.md)di [Panoramica](what-is-app-attach.md) . In caso contrario, iniziare a [impostare la connessione app](app-attach.md).