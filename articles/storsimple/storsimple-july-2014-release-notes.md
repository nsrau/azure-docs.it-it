---
title: Note sulla versione di rilascio di StorSimple 8000 | Microsoft Docs
description: "Vengono descritte le nuove funzionalità, i problemi aperti e le soluzioni alternative disponibili per la versione di luglio 2014 di Microsoft Azure StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 12f1796e-37c3-42b4-b997-a84fc1950c20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 303cdffa15fdfe9b83d0612edecafc6943d218f3
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Note sulla versione di rilascio di StorSimple serie 8000 - Luglio 2014
## <a name="overview"></a>Panoramica
Le seguenti note sulla versione identificano i problemi critici aperti per la versione di disponibilità generale di luglio 2014 di StorSimple serie 8000 di Microsoft Azure StorSimple. Questa versione corrisponde alla versione del software 6.3.9600.17215.  

A meno che non sia diversamente specificato, queste note sulla versione si applicano a tutti i modelli del dispositivo StorSimple. Le note sulla versione vengono aggiornate continuamente; i problemi critici che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire la soluzione Microsoft Azure StorSimple, tenere presenti le informazioni seguenti.  

## <a name="known-issues-in-this-release"></a>Problemi noti in questa versione
Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.  

| No. | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- | --- |
| 1 |Ripristino delle impostazioni predefinite |In alcuni casi, quando si esegue un ripristino delle impostazioni predefinite, il dispositivo StorSimple potrebbe bloccarsi e l’utente potrebbe visualizzare il messaggio: **Ripristino delle impostazioni predefinite in corso (fase 8)**. Ciò si verifica se si preme CTRL + C mentre il cmdlet è in esecuzione. |Non premere CTRL + C dopo l'avvio di un ripristino delle impostazioni predefinite. Se si è già in questo stato, contattare il supporto tecnico Microsoft per i passaggi successivi. |Sì |No |
| 2 |Quorum disco |In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 è disconnessa generando un’assenza di quorum disco, il pool di archiviazione sarà offline  e rimarrà in tale stato anche se i dischi vengono riconnessi. |Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. |Sì |No |
| 3 |Errori di snapshot nel cloud |In rari casi, uno snapshot cloud potrebbe non riuscire a causa dell’errore **Raggiunto il limite massimo di backup**. Ciò si verifica se si superano i 255 cloni online sullo stesso dispositivo, dallo stesso volume originale eliminato. | |Sì |Sì |
| 4 |ID controller non corretto |Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. |Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. |Sì |No |
| 5 |Grafici di monitoraggio del dispositivo |Nel servizio StorSimple Manager, i grafici di monitoraggio del dispositivo non funzionano quando l’autenticazione di base o NTLM è abilitata nella configurazione del server proxy per il dispositivo. |Modificare la configurazione del proxy Web per il dispositivo registrato con il servizio StorSimple Manager in modo che l'autenticazione sia impostata su NESSUNA. A tale scopo, eseguire il cmdlet Set-HcsWebProxy di Windows PowerShell per StorSimple. |Sì |Sì |
| 6 |Account di archiviazione |L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente. | |Sì |Sì |
| 7 |Failback |Il failback entro 24 ore di ripristino di emergenza (DR) non è supportato. | |Sì |No |
| 8 |Failover del dispositivo |I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. Il failover da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di Azure classico. | |Sì |No |
| 9 |Installazione |Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | |Sì |No |
| 10 |Interfacce di rete |Nella versione precedente, le interfacce di rete DATA 2 DATA 3 sono state scambiate nel software. |Se è necessario configurare queste interfacce, contattare il supporto tecnico Microsoft. |Sì |No |


