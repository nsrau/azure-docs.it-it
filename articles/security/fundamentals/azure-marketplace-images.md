---
title: Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace | Microsoft Docs
description: Questo articolo contiene raccomandazioni per le immagini incluse in Marketplace
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795817"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace

L'immagine deve soddisfare questi consigli sulla configurazione di sicurezza. Ciò consente di mantenere un livello elevato di sicurezza per le immagini delle soluzioni di partner in Azure Marketplace.

Eseguire sempre un rilevamento delle vulnerabilità di sicurezza sull'immagine prima dell'invio. Se si rileva una vulnerabilità di sicurezza nella propria immagine pubblicata, è necessario informare i clienti in modo tempestivo sia la vulnerabilità che la modalità di correzione.

## <a name="open-source-based-images"></a>Immagini basate su open source

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Controllo**                                                                                                                                                                                                                                                                              |
| Sicurezza                                                     | Installare tutte le patch di sicurezza più recenti per la distribuzione di Linux.                                                                                                                                                                                                              |
| Sicurezza                                                     | Seguire le linee guida di settore per proteggere l'immagine di macchina virtuale per la distribuzione Linux specifica.                                                                                                                                                                                     |
| Sicurezza                                                     | Limitare la superficie di attacco mantenendo un footprint minimo soltanto con ruoli, funzionalità, servizi e porte di rete Windows Server necessari.                                                                                                                                               |
| Sicurezza                                                     | Analizzare il codice sorgente e l'immagine di macchine virtuali risultante per ricercare eventuale malware.                                                                                                                                                                                                                                   |
| Sicurezza                                                     | L'immagine del disco rigido virtuale include solo gli account bloccati necessari che non dispongono di password predefinite che consentono l'accesso interattivo. nessun back-door.                                                                                                                                           |
| Sicurezza                                                     | Disabilitare le regole del firewall a meno che l'applicazione non si basi su di essi, ad esempio un dispositivo firewall.                                                                                                                                                                             |
| Sicurezza                                                     | Rimuovere tutte le informazioni riservate dall'immagine del disco rigido virtuale, ad esempio le chiavi SSH di test, il file host noto, i file di log e i certificati non necessari.                                                                                                                                       |
| Sicurezza                                                     | Evitare di usare LVM.                                                                                                                                                                                                                                            |
| Sicurezza                                                     | Includere le versioni più recenti delle librerie obbligatorie: </br> - OpenSSL v1.0 o versioni successive </br> - Python 2.5 o versioni successive (si consiglia Python 2.6 o versioni successive) </br> - Pacchetto pyasn1 per Python, se non già installato </br> - d.OpenSSL v 1.0 o versioni successive                                                                |
| Sicurezza                                                     | Cancella le voci della cronologia Bash/Shell.                                                                                                                                                                                                                                             |
| Rete                                                   | Includere il server SSH per impostazione predefinita. Impostare SSH Keep Alive sulla configurazione sshd con l'opzione seguente: ClientAliveInterval 180.                                                                                                                                                        |
| Rete                                                   | Rimuovere qualsiasi configurazione di rete personalizzata dall'immagine. Eliminare il `rm /etc/resolv.conf`di risoluzione. conf:.                                                                                                                                                                                |
| Distribuzione                                                   | Installare la versione più recente dell'agente Linux di Azure.</br> -Installare usando il pacchetto RPM o deb.  </br> - È possibile anche usare il processo di installazione manuale, ma è consigliabile e preferibile usare i pacchetti di installazione. </br> - Se si esegue l'installazione manuale dell'agente dal repository GitHub, copiare prima il file `waagent` in `/usr/sbin` ed eseguirlo (come radice): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Il file di configurazione dell'agente viene inserito in `/etc/waagent.conf`. |
| Distribuzione                                                   | Assicurarsi che il supporto tecnico di Azure possa fornire ai partner l'output della console seriale quando necessario e fornire un timeout adeguato per il montaggio del disco del sistema operativo dall'archiviazione cloud. Aggiungere i parametri seguenti alla riga di avvio del kernel image: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Distribuzione                                                   | Nessuna partizione swap nel disco del sistema operativo. Lo swap può essere richiesto per la creazione del disco delle risorse locali dall'agente Linux.         |
| Distribuzione                                                   | Creare una singola partizione radice per il disco del sistema operativo.      |
| Distribuzione                                                   | Solo sistema operativo a 64 bit.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Immagini basate su Windows Server

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Controllo**                                                                                                                                                                |
| Sicurezza                                                         | Usare un'immagine base del sistema operativo protetta. Il disco rigido virtuale usato per l'origine di un'immagine basata su Windows Server deve provenire da immagini del sistema operativo Windows Server fornite tramite Microsoft Azure. |
| Sicurezza                                                         | Installare tutti gli aggiornamenti della sicurezza più recenti.                                                                                                                                     |
| Sicurezza                                                         | Le applicazioni non devono dipendere da nomi utente limitati come amministratore, radice o amministratore.                                                                |
| Sicurezza                                                         | Abilitare Crittografia unità BitLocker per i dischi rigidi del sistema operativo e i dischi rigidi.                                                             |
| Sicurezza                                                         | Limitare la superficie di attacco mantenendo un footprint minimo abilitando soltanto ruoli, funzionalità, servizi e porte di rete Windows Server necessari.                         |
| Sicurezza                                                         | Analizzare il codice sorgente e l'immagine di macchine virtuali risultante per ricercare eventuale malware.                                                                                                                     |
| Sicurezza                                                         | Impostare l'aggiornamento automatico degli aggiornamenti di sicurezza delle immagini Windows Server.                                                                                                                |
| Sicurezza                                                         | L'immagine del disco rigido virtuale include solo gli account bloccati necessari che non dispongono di password predefinite che consentono l'accesso interattivo. nessun back-door.                             |
| Sicurezza                                                         | Disabilitare le regole del firewall a meno che l'applicazione non si basi su di essi, ad esempio un dispositivo firewall.                                                               |
| Sicurezza                                                         | Rimuovere tutte le informazioni riservate dall'immagine del disco rigido virtuale, inclusi i file host, i file di log e i certificati non necessari.                                              |
| Distribuzione                                                       | Solo sistema operativo a 64 bit.                            |

Anche se l'organizzazione non ha immagini in Azure Marketplace, provare a controllare le configurazioni delle immagini Windows e Linux in base a questi consigli.

## <a name="contacting-customers"></a>Contattare i clienti

Per identificare i clienti e i relativi messaggi di posta elettronica di contatto:

1.  Nella barra di sinistra di portale Cloud Partner selezionare **Insights (informazioni dettagliate**).
2.  Nella scheda **Orders and Usage** usare i campi data di **inizio** e **Data di fine** per eseguire query sull'utilizzo entro l'intervallo di date richiesto. Questo mostra le sottoscrizioni di Azure usate per l'offerta su base giornaliera. Esportare questi dati. 
3.  Analogamente, nella scheda **Customer** eseguire una query ed esportare la base clienti.
4.  Trovare la corrispondenza con l'ID sottoscrizione dal passaggio 2 all'ID sottoscrizione del passaggio 3 per trovare le informazioni necessarie sul cliente.
