---
title: Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace | Microsoft Docs
description: Questo articolo contiene raccomandazioni per le immagini incluse in Marketplace
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: fa521b81c95f7c0556b082e5487848ef4d7ecaf7
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615889"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace

È consigliabile che ogni soluzione sia conforme alle raccomandazioni per la configurazione di sicurezza seguenti. Ciò consente di mantenere un livello elevato di sicurezza per le immagini delle soluzioni di partner in Azure Marketplace.

Queste raccomandazioni possono anche essere utili per le organizzazioni che non dispongono di immagini in Azure Marketplace. È consigliabile controllare le configurazioni delle immagini Windows e Linux dell'azienda rispetto alle linee guida riportate nelle tabelle seguenti:

## <a name="open-source-based-images"></a>Immagini basate su open source

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Controllo**                                                                                                                                                                                                                                                                              |
| Security                                                     | Vengono installate tutte le patch di protezione più recenti per la distribuzione Linux.                                                                                                                                                                                                              |
| Security                                                     | Sono state seguite le linee guida del settore per proteggere l'immagine di macchine virtuali per la distribuzione Linux specifica.                                                                                                                                                                                     |
| Security                                                     | Limitare la superficie di attacco mantenendo un footprint minimo soltanto con ruoli, funzionalità, servizi e porte di rete Windows Server necessari.                                                                                                                                               |
| Security                                                     | Analizzare il codice sorgente e l'immagine di macchine virtuali risultante per ricercare eventuale malware.                                                                                                                                                                                                                                   |
| Security                                                     | L'immagine del disco rigido virtuale include solo gli account bloccati necessari, che non dispongono di password predefinite che potrebbero consentire l'accesso interattivo; nessun backdoor.                                                                                                                                           |
| Security                                                     | Le regole del firewall vengono disabilitate, a meno che l'applicazione non si basi a livello funzionale su di esse, ad esempio un'appliance firewall.                                                                                                                                                                             |
| Security                                                     | Tutte le informazioni riservate sono stato rimosse dall'immagine di disco rigido virtuale, ad esempio le chiavi SSH di test, i file hosts noti, i file di log e i certificati non necessari.                                                                                                                                       |
| Security                                                     | È consigliabile non usare la gestione dei volumi logici (LVM).                                                                                                                                                                                                                                            |
| Security                                                     | È necessario includere le versioni più recenti delle librerie necessarie: </br> - OpenSSL v1.0 o versioni successive </br> - Python 2.5 o versioni successive (si consiglia Python 2.6 o versioni successive) </br> - Pacchetto pyasn1 per Python, se non già installato </br> - d.OpenSSL v 1.0 o versioni successive                                                                |
| Security                                                     | Le voci della cronologia Bash/Shell devono essere cancellate                                                                                                                                                                                                                                             |
| Rete                                                   | Il server SSH deve essere incluso per impostazione predefinita. Impostare SSH keep alive sulla configurazione per mantenere attive le sessioni SSH con l'opzione seguente: ClientAliveInterval 180                                                                                                                                                        |
| Rete                                                   | L'immagine non deve contenere configurazioni di rete personalizzate. Eliminare resolv.conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Distribuzione                                                   | Deve essere installato l'agente Linux di Azure più recente </br> - L'agente deve essere installato mediante il pacchetto Deb o RPM.  </br> - È possibile anche usare il processo di installazione manuale, ma è consigliabile e preferibile usare i pacchetti di installazione. </br> - Se si esegue l'installazione manuale dell'agente dal repository GitHub, copiare prima il file `waagent` in `/usr/sbin` ed eseguirlo (come radice): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Il file di configurazione dell'agente viene inserito in `/etc/waagent.conf`.    |
| Distribuzione                                                   | Assicura che il supporto di Azure possa fornire ai partner l'output della console seriale quando è necessario e possa consentire il timeout adeguato per il montaggio del disco del sistema operativo dall'archiviazione cloud. L'immagine deve essere aggiunta ai parametri seguenti alla riga di comando per l'avvio del kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Distribuzione                                                   | Nessuna partizione swap nel disco del sistema operativo. Lo swap può essere richiesto per la creazione del disco delle risorse locali dall'agente Linux.         |
| Distribuzione                                                   | È consigliabile che venga creata una singola partizione radice per il disco del sistema operativo.      |
| Distribuzione                                                   | Solo sistema operativo a 64 bit.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Immagini basate su Windows Server

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Controllo**                                                                                                                                                                |
| Security                                                         | Usare un'immagine base del sistema operativo protetta. Il disco rigido virtuale usato per l'origine di un'immagine basata su Windows Server deve provenire da immagini del sistema operativo Windows Server fornite tramite Microsoft Azure. |
| Security                                                         | Installare tutti gli aggiornamenti della sicurezza più recenti.                                                                                                                                     |
| Security                                                         | Le applicazioni non devono avere una dipendenza dai nomi utente con restrizioni, ad esempio Administrator, radice e amministratore.                                                                |
| Security                                                         | Crittografia unità BitLocker non è supportata sul disco rigido del sistema operativo. È possibile usare BitLocker nei dischi di dati.                                                            |
| Security                                                         | Limitare la superficie di attacco mantenendo un footprint minimo abilitando soltanto ruoli, funzionalità, servizi e porte di rete Windows Server necessari.                         |
| Security                                                         | Analizzare il codice sorgente e l'immagine di macchine virtuali risultante per ricercare eventuale malware.                                                                                                                     |
| Security                                                         | Impostare l'aggiornamento automatico degli aggiornamenti di sicurezza delle immagini Windows Server.                                                                                                                |
| Security                                                         | L'immagine del disco rigido virtuale include solo gli account bloccati necessari, che non dispongono di password predefinite che potrebbero consentire l'accesso interattivo; nessun backdoor.                             |
| Security                                                         | Le regole del firewall vengono disabilitate, a meno che l'applicazione non si basi a livello funzionale su di esse, ad esempio un'appliance firewall.                                                               |
| Security                                                         | Tutte le informazioni riservate sono state rimosse dall'immagine del disco rigido virtuale. Ad esempio, il file host, i file di log e i certificati non necessari devono essere rimossi.                                              |
| Distribuzione                                                       | Solo sistema operativo a 64 bit.                            |
