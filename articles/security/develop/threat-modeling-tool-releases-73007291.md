---
title: Microsoft Threat Modeling Tool versione 07/29/2020-Azure
description: Documentazione delle note sulla versione per Threat Modeling Tool versione 7.3.00729.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: fc343f292fa32ed2db67dd74aba7a66dbc00d6ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317838"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Versione di aggiornamento Threat Modeling Tool 7.3.00729.1-07/29/2020

La versione 7.3.00729.1 del Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 29 2020 luglio e contiene le modifiche seguenti:

- Correzioni di bug
 
## <a name="known-issues"></a>Problemi noti

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Errori correlati alla deserializzazione del file TMT7. Application

#### <a name="issue"></a>Problema

Alcuni clienti hanno segnalato la ricezione del messaggio di errore seguente durante il download del Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Questo errore si verifica perché alcuni browser non supportano in modo nativo l'installazione ClickOnce. In questi casi il file dell'applicazione ClickOnce viene scaricato sul disco rigido dell'utente.

#### <a name="workaround"></a>Soluzione alternativa

Questo errore continuerà a essere visualizzato se la Threat Modeling Tool viene avviata facendo doppio clic sul file TMT7. Application. Tuttavia, dopo aver ignorato l'errore, lo strumento funzionerà normalmente. Anziché avviare il Threat Modeling Tool facendo doppio clic sul file TMT7. Application, è necessario che gli utenti utilizzino i collegamenti creati nel menu Windows durante l'installazione per avviare il Threat Modeling Tool.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
