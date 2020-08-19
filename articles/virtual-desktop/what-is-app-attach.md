---
title: Panoramica di MSIX per il desktop virtuale Windows-Azure
description: Che cos'è la connessione all'app MSIX? Vedere questo articolo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556280"
---
# <a name="what-is-msix-app-attach"></a>Che cos'è la connessione all'app MSIX?

MSIX è un nuovo formato per la creazione di pacchetti che offre molte funzionalità destinate a migliorare l'esperienza di creazione pacchetti per tutte le app di Windows. Per altre informazioni su MSIX, vedere [Panoramica di MSIX](/windows/msix/overview).

La connessione app MSIX è un modo per distribuire applicazioni MSIX sia per le macchine fisiche che per quelle virtuali. Tuttavia, la connessione all'app MSIX è diversa da quella dei normali MSIX perché viene eseguita in particolare per desktop virtuale di Windows. Questo articolo descrive la connessione dell'app MSIX e le operazioni che è possibile eseguire per l'utente.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Opzioni di recapito delle applicazioni nel desktop virtuale di Windows

È possibile distribuire le app in desktop virtuale di Windows tramite uno dei metodi seguenti:

- Inserire app in un'immagine master
- Usare strumenti come SCCM o Intune per la gestione centrale
- Provisioning dinamico delle app (AppV, VMWare AppVolumes o Citrix AppLayering)
- Creazione di strumenti o script personalizzati con Microsoft e uno strumento di terze parti

## <a name="what-does-msix-app-attach-do"></a>Che cosa fa la connessione all'app MSIX?

In una distribuzione di desktop virtuali Windows, la connessione all'app MSIX può:

- Consente di creare la separazione tra i dati utente, il sistema operativo e le app usando [contenitori MSIX](/windows/msix/msix-container).
- Rimuovere la necessità di riconfezionamento quando si distribuiscono applicazioni in modo dinamico.
- Ridurre il tempo necessario per l'accesso di un utente.
- Ridurre i requisiti e i costi dell'infrastruttura.

La connessione all'app MSIX deve essere applicabile all'esterno di VDI o SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Sovrapposizione di app tradizionali rispetto alla connessione di app MSIX

La tabella seguente mette a confronto la funzionalità chiave della connessione app di MSIX e la sovrapposizione delle app.

| Feature | Sovrapposizione di app tradizionali  | Connessione dell'app MSIX  |
|-----|-----------------------------|--------------------|
| Formato               | Diverse tecnologie di sovrapposizione delle app richiedono formati proprietari diversi. | Funziona con il formato nativo per la creazione di pacchetti MSIX.        |
| Reimballaggio del sovraccarico | I formati proprietari richiedono la sequenziazione e la creazione di pacchetti per ogni aggiornamento.         | Le app pubblicate come MSIX non richiedono la ricreazione del pacchetto. Tuttavia, se il pacchetto MSIX non è disponibile, viene comunque applicato il sovraccarico di Repackaging. |
| Ecosistema            | N/d (ad esempio, i fornitori non spediscono App-V)  | MSIX è la tecnologia principale di Microsoft che i partner ISV principali e le app interne come Office stanno adottando. È possibile usare MSIX sia per i desktop virtuali che per i computer Windows fisici. |
| Infrastruttura       | Infrastruttura aggiuntiva necessaria (server, client e così via) | Solo archiviazione   |
| Amministrazione       | Richiede manutenzione e aggiornamento   | Semplifica gli aggiornamenti delle app |
| Esperienza utente      | Influisca sull'ora di accesso dell'utente. Esiste un limite tra lo stato del sistema operativo, lo stato dell'app e i dati utente.  | Le app recapitate non sono distinguibili dalle applicazioni installate localmente. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla connessione all'app MSIX, vedere il [Glossario](app-attach-glossary.md) e le [domande frequenti](app-attach-faq.md). In caso contrario, iniziare a [impostare la connessione app](app-attach.md).
