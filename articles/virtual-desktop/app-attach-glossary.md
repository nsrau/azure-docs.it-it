---
title: Glossario per la connessione di app desktop virtuali Windows MSIX-Azure
description: Un glossario di termini e concetti relativi all'app MSIX.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7132eae073f3d53a104536076ae801ec9ff93e5f
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518669"
---
# <a name="msix-app-attach-glossary"></a>Glossario per il fissaggio dell'app MSIX

Questo articolo è un elenco di definizioni per i termini chiave e i concetti relativi alla connessione app MSIX.

## <a name="msix-container"></a>Contenitore MSIX

Un contenitore MSIX è il punto in cui vengono eseguite le app MSIX. Per altre informazioni, vedere [contenitori MSIX](/windows/msix/msix-container).

## <a name="msix-application"></a>Applicazione MSIX 

Applicazione archiviata in un oggetto. File MSIX.

## <a name="msix-package"></a>Pacchetto MSIX 

Un pacchetto MSIX è un file o un'applicazione MSIX.

## <a name="msix-share"></a>Condivisione MSIX

Una condivisione MSIX è una condivisione di rete che include pacchetti MSIX espansi. Le condivisioni MSIX supportano SMB 3 o versioni successive. Le applicazioni vengono preparate da questa condivisione MSIX senza dover spostare i file dell'applicazione nell'unità di sistema.

## <a name="msix-image"></a>Immagine di MSIX

Un'immagine MSIX è un file VHD, VHDx o CIM che contiene una o più applicazioni in pacchetto MSIX. Ogni applicazione viene distribuita nell'immagine MSIX usando lo strumento MSIXMGR.

## <a name="repackage"></a>Riconfezionare

Il riassemblaggio accetta un'applicazione non MSIX e la converte in MSIX mediante lo strumento di creazione di pacchetti MSIX (MPT). Per altre informazioni, vedere [Panoramica dello strumento di creazione pacchetti MSIX](/windows/msix/packaging-tool/tool-overview).

## <a name="expand-an-msix-package"></a>Espandi un pacchetto MSIX

L'espansione di un pacchetto MSIX è un processo in più passaggi. L'espansione accetta il file MSIX e inserisce il contenuto in un file VHD (x) o CIM. 

Per espandere un pacchetto MSIX:

1. Ottenere un pacchetto MSIX (file MSIX).
2. Rinominare il file MSIX in un file con estensione zip.
3. Decomprimere il file zip risultante in una cartella.
4. Creare un disco rigido virtuale con le stesse dimensioni della cartella.
5. Montare il disco rigido virtuale.
6. Inizializzare un disco.
7. Creare una partizione.
8. Formattare la partizione.
9. Copiare il contenuto decompresso nel disco rigido virtuale.
10. Utilizzare lo strumento MSIXMGR per applicare ACL al contenuto del disco rigido virtuale.
11. Smontare il disco rigido virtuale (x) o [CIM](#cim).

## <a name="upload-an-msix-package"></a>Caricare un pacchetto MSIX 

Il caricamento di un pacchetto MSIX comporta il caricamento del disco rigido virtuale (x) o [CIM](#cim) che contiene un pacchetto MSIX espanso nella condivisione MSIX.

Nel desktop virtuale di Windows, i caricamenti avvengono una volta per ogni condivisione MSIX. Una volta caricato un pacchetto, tutti i pool host nella stessa sottoscrizione possono farvi riferimento.

## <a name="add-an-msix-package"></a>Aggiungere un pacchetto MSIX

In desktop virtuale di Windows, l'aggiunta di un pacchetto MSIX lo collega a un pool host.

## <a name="publish-an-msix-package"></a>Pubblicare un pacchetto MSIX 

Nel desktop virtuale di Windows è necessario assegnare un pacchetto MSIX pubblicato a un utente o a un gruppo di utenti di servizi di Dominio di Active Directory (AD DS) o Azure Active Directory (Azure AD).

## <a name="staging"></a>Staging

La gestione temporanea prevede due elementi:

- Montaggio del disco rigido virtuale (x) o [CIM](#cim) nella macchina virtuale.
- Notifica al sistema operativo che il pacchetto MSIX è disponibile per la registrazione.

## <a name="registration"></a>Registrazione

La registrazione rende disponibile un pacchetto MSIX di gestione temporanea per gli utenti. La registrazione è in base ai singoli utenti. Se non è stata registrata in modo esplicito un'app per l'utente specifico, non sarà possibile eseguire l'app.

Esistono due tipi di registrazione: normale e ritardata.

### <a name="regular-registration"></a>Registrazione regolare

Durante la registrazione regolare, ogni applicazione assegnata a un utente viene registrata completamente. La registrazione viene eseguita durante l'accesso dell'utente alla sessione, che può influisca sul tempo necessario per iniziare a usare desktop virtuale di Windows.

### <a name="delayed-registration"></a>Registrazione ritardata

Nella registrazione posticipata, ogni applicazione assegnata all'utente è registrata solo parzialmente. Con registrazione parziale si intende che il riquadro del menu Start e le associazioni di file sono registrate. La registrazione si verifica quando l'utente accede alla propria sessione, in modo da avere un effetto minimo sul tempo necessario per iniziare a usare desktop virtuale di Windows. La registrazione viene completata solo quando l'utente esegue l'applicazione nel pacchetto MSIX.

La registrazione ritardata è attualmente la configurazione predefinita per la connessione all'app MSIX.

## <a name="deregistration"></a>Annullamento della registrazione

La deregistrazione rimuove un pacchetto MSIX registrato ma non in esecuzione per un utente. L'annullamento della registrazione si verifica quando l'utente si disconnette dalla sessione. Durante l'annullamento della registrazione, l'associazione dell'app MSIX inserisce i dati dell'applicazione specifici dell'utente nel profilo utente locale.

## <a name="destage"></a>Rimozione gestione temporanea

La degestione temporanea notifica al sistema operativo che un'applicazione o un pacchetto MSIX che attualmente non è in esecuzione e non è stato eseguito temporaneamente per nessun utente può essere smontato. Questa operazione rimuove tutti i riferimenti ad esso nel sistema operativo.

## <a name="cim"></a>CIM

CIM è una nuova estensione di file associata a composite image file System (CimFS). Il montaggio e lo smontaggio dei file CIM sono più veloci dei file VHD. CIM usa anche meno CPU e memoria rispetto al disco rigido virtuale.

Un file CIM è un file con un. Estensione CIM che contiene i metadati e almeno sei file aggiuntivi che contengono dati effettivi. I file all'interno del file CIM non hanno estensioni. Nella tabella seguente è riportato un elenco di file di esempio disponibili all'interno di un CIM:

| Nome file | Estensione | Dimensione |
|-----------|-----------|------|
| VSC | CIM | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | N/D | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | N/D | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | N/D | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | N/D | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | N/D | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | N/D | 264.132 KB |

La tabella seguente rappresenta un confronto delle prestazioni tra VHD e CimFS. Questi numeri sono il risultato di un'esecuzione dei test con file di 500 300 MB in ogni formato eseguito in un computer DSv4.

|  Specifiche                          | VHD                     | CimFS   |
|---------------------------------|--------------------------|-----------|
| Tempo di montaggio medio     | 356 ms                     | 255 ms      |
| Tempo di smontaggio medio   | 1615 MS                    | 36 ms       |
| Memory consumption | 6% (di 8 GB)                      | 2% (di 8 GB)       |
| CPU (conteggio picchi)          | Massimo più volte | Nessun effetto |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla connessione all'app MSIX, vedere la [Panoramica](what-is-app-attach.md) e le [domande frequenti](app-attach-faq.md). In caso contrario, iniziare a [impostare la connessione app](app-attach.md).
