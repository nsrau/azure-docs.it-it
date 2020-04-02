---
title: Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace | Microsoft Docs
description: Questo articolo contiene raccomandazioni per le immagini incluse in Marketplace
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548663"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace

L'immagine deve soddisfare questi consigli di configurazione della sicurezza. Ciò consente di mantenere un livello elevato di sicurezza per le immagini delle soluzioni di partner in Azure Marketplace.

Eseguire sempre un rilevamento di vulnerabilità di sicurezza sull'immagine prima dell'invio. Se si rileva una vulnerabilità di sicurezza nella propria immagine pubblicata, è necessario informare i clienti in modo tempestivo sia della vulnerabilità che di come correggerla.

## <a name="open-source-based-images"></a>Immagini basate su open source

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Category**                                                 | **Verifica**                                                                                                                                                                                                                                                                              |
| Security                                                     | Installare tutte le patch di sicurezza più recenti per la distribuzione Linux.                                                                                                                                                                                                              |
| Security                                                     | Seguire le linee guida del settore per proteggere l'immagine della macchina virtuale per la distribuzione Linux specifica.                                                                                                                                                                                     |
| Security                                                     | Limitare la superficie di attacco mantenendo un footprint minimo soltanto con ruoli, funzionalità, servizi e porte di rete Windows Server necessari.                                                                                                                                               |
| Security                                                     | Analizzare il codice sorgente e l'immagine di macchine virtuali risultante per ricercare eventuale malware.                                                                                                                                                                                                                                   |
| Security                                                     | L'immagine VHD include solo gli account bloccati necessari che non dispongono di password predefinite che consentirebbero l'accesso interattivo; senza porte posteriori.                                                                                                                                           |
| Security                                                     | Disattivare le regole del firewall a meno che l'applicazione non si basa funzionalmente su di esse, ad esempio un'appliance firewall.                                                                                                                                                                             |
| Security                                                     | Rimuovere tutte le informazioni riservate dall'immagine del disco rigido virtuale, ad esempio le chiavi SSH di prova, i file host noti, i file di log e i certificati non necessari.                                                                                                                                       |
| Security                                                     | Evitare di utilizzare LVM.                                                                                                                                                                                                                                            |
| Security                                                     | Includi le versioni più recenti delle librerie richieste: </br> - OpenSSL v1.0 o versioni successive </br> - Python 2.5 o versioni successive (si consiglia Python 2.6 o versioni successive) </br> - Pacchetto pyasn1 per Python, se non già installato </br> - d.OpenSSL v 1.0 o versioni successive                                                                |
| Security                                                     | Cancellare le voci della cronologia Bash/Shell.                                                                                                                                                                                                                                             |
| Rete                                                   | Includere il server SSH per impostazione predefinita. Impostare SSH keep alive su sshd config con la seguente opzione: ClientAliveInterval 180.                                                                                                                                                        |
| Rete                                                   | Rimuovere qualsiasi configurazione di rete personalizzata dall'immagine. Eliminare il file resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Distribuzione                                                   | Installare l'agente Linux di Azure più recente.</br> - Eseguire l'installazione utilizzando il pacchetto RPM o Deb.  </br> - È possibile anche usare il processo di installazione manuale, ma è consigliabile e preferibile usare i pacchetti di installazione. </br> - Se si esegue l'installazione manuale dell'agente dal repository GitHub, copiare prima il file `waagent` in `/usr/sbin` ed eseguirlo (come radice): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Il file di configurazione dell'agente viene inserito in `/etc/waagent.conf`. |
| Distribuzione                                                   | Assicurarsi che il supporto di Azure sia in grado di fornire ai partner l'output della console seriale quando necessario e di fornire un timeout adeguato per il montaggio del disco del sistema operativo dall'archiviazione cloud. Aggiungere i seguenti parametri all'immagine `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`Linea di avvio kernel: . |
| Distribuzione                                                   | Nessuna partizione swap nel disco del sistema operativo. Lo swap può essere richiesto per la creazione del disco delle risorse locali dall'agente Linux.         |
| Distribuzione                                                   | Creare una singola partizione radice per il disco del sistema operativo.      |
| Distribuzione                                                   | Solo sistema operativo a 64 bit.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Immagini basate su Windows Server

|||
|-------------| -------------------------|
| **Category**                                                     | **Verifica**                                                                                                                                                                |
| Security                                                         | Usare un'immagine base del sistema operativo protetta. Il disco rigido virtuale usato per l'origine di un'immagine basata su Windows Server deve provenire da immagini del sistema operativo Windows Server fornite tramite Microsoft Azure. |
| Security                                                         | Installare tutti gli aggiornamenti della sicurezza più recenti.                                                                                                                                     |
| Security                                                         | Le applicazioni non devono dipendere da nomi utente con restrizioni come amministratore, root o admin.                                                                |
| Security                                                         | Abilitare Crittografia unità BitLocker sia per i dischi rigidi del sistema operativo che per i dischi rigidi dati.                                                             |
| Security                                                         | Limitare la superficie di attacco mantenendo un footprint minimo abilitando soltanto ruoli, funzionalità, servizi e porte di rete Windows Server necessari.                         |
| Security                                                         | Analizzare il codice sorgente e l'immagine di macchine virtuali risultante per ricercare eventuale malware.                                                                                                                     |
| Security                                                         | Impostare l'aggiornamento automatico degli aggiornamenti di sicurezza delle immagini Windows Server.                                                                                                                |
| Security                                                         | L'immagine VHD include solo gli account bloccati necessari che non dispongono di password predefinite che consentirebbero l'accesso interattivo; senza porte posteriori.                             |
| Security                                                         | Disattivare le regole del firewall a meno che l'applicazione non si basa funzionalmente su di esse, ad esempio un'appliance firewall.                                                               |
| Security                                                         | Rimuovere tutte le informazioni riservate dall'immagine VHD, inclusi i file HOSTS, i file di registro e i certificati non necessari.                                              |
| Distribuzione                                                       | Solo sistema operativo a 64 bit.                            |

Anche se l'organizzazione non dispone di immagini nel marketplace di Azure, è consigliabile controllare le configurazioni delle immagini Windows e Linux rispetto a questi consigli.

## <a name="contacting-customers"></a>Contattare i clienti

Per identificare i clienti e le loro e-mail di contatto:

1.  Nel portale Cloud Partner, sul riquadro sinistro, selezionare **Insights**.
2.  Nella scheda **Ordini e utilizzo** utilizzare i campi Data di **inizio** e Data **di fine** per eseguire query sull'utilizzo all'interno dell'intervallo di date richiesto. Vengono illustrate le sottoscrizioni di Azure usate per l'offerta su base giornaliera. Esportare questi dati. 
3.  Analogamente, nella scheda **Cliente,** eseguire una query ed esportare la base clienti.
4.  Associare l'ID sottoscrizione dal passaggio 2 all'ID sottoscrizione del passaggio 3 per trovare le informazioni necessarie sul cliente.
