---
title: Connettività SSL-database di Azure per MySQL
description: Informazioni per la configurazione del Database di Azure per MySQL e delle applicazioni associate per usare correttamente le connessioni SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d677e7c80d98b15a638b00c5b8f4d390a492c7fa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770816"
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
