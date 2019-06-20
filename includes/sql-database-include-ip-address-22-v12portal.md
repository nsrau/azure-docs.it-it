---
title: Regole del firewall a livello di server
description: Regole del firewall a livello di server
keywords: connessione SQL, stringa di connessione
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180226"
---
1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Nell'elenco a sinistra selezionare **Tutti i servizi**.

3. Scorrere e selezionare **SQL Server**.

    ![Trovare il server di Database SQL di Azure nel portale di][b21-FindServerInPortal]
5. Nella casella di testo di filtro, iniziare a digitare il nome del server. Viene visualizzata la riga.

6. Selezionare la riga per il server. Viene visualizzato un pannello per il server.

7. Nel pannello del server, selezionare **Impostazioni**.

8. Selezionare **Firewall**.

    ![Selezionare Impostazioni > Firewall][b31-SettingsFirewallNavig]
9. Selezionare **Aggiungi IP client**. Digitare un nome per la nuova regola nella prima casella di testo.

10. Digitare i valori di indirizzo IP minimo e massimo per l'intervallo che si desidera abilitare.

    * Può essere utile impostare come fine del valore minimo **.0** e come fine del valore massimo su **.255**.

11. Selezionare **Salva**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
