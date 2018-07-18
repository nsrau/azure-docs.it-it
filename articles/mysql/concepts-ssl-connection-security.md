---
title: Connettività SSL per Database di Azure per MySQL
description: Informazioni per la configurazione del Database di Azure per MySQL e delle applicazioni associate per usare correttamente le connessioni SSL
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee7e0ec8524d66ee89cf7b2c4d44b70efa784f8f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265062"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Connettività SSL nel Database di Azure per MySQL
Il Database di Azure per MySQL supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="default-settings"></a>Impostazioni predefinite
Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MySQL.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile. 

Per impostazione predefinita, l'opzione di applicazione delle connessioni SSL è attiva quando si esegue il provisioning di un nuovo Database di Azure per il server MySQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni**, selezionare **Stringhe di connessione**. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

Per informazioni su come abilitare o disabilitare la connessione SSL durante lo sviluppo dell'applicazione, consultare [Come configurare SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Passaggi successivi
[Raccolte connessioni per il Database di Azure per MySQL](concepts-connection-libraries.md)
