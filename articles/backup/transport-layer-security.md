---
title: Transport Layer Security in backup di Azure
description: Informazioni su come abilitare backup di Azure per l'uso del protocollo di crittografia Transport Layer Security (TLS) per proteggere i dati durante il trasferimento in rete.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327118"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security in backup di Azure

Transport Layer Security (TLS) è un protocollo di crittografia che garantisce la protezione dei dati durante il trasferimento in rete. Backup di Azure usa la sicurezza a livello di trasporto per proteggere la privacy dei dati di backup trasferiti. Questo articolo descrive i passaggi per abilitare il protocollo TLS 1,2, che garantisce una maggiore sicurezza rispetto alle versioni precedenti.

## <a name="earlier-versions-of-windows"></a>Versioni precedenti di Windows

Se nel computer sono in esecuzione versioni precedenti di Windows, è necessario installare gli aggiornamenti corrispondenti indicati di seguito ed è necessario applicare le modifiche del registro di sistema documentate negli articoli della KB.

|Sistema operativo  |articolo KB |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>L'aggiornamento installerà i componenti del protocollo richiesti. Dopo l'installazione, è necessario apportare le modifiche alla chiave del registro di sistema citate negli articoli della Knowledge fine per abilitare correttamente i protocolli richiesti.

## <a name="verify-windows-registry"></a>Verifica registro di sistema di Windows

### <a name="configuring-schannel-protocols"></a>Configurazione di protocolli SChannel

Le chiavi del registro di sistema seguenti assicurano che il protocollo TLS 1,2 sia abilitato a livello di componente SChannel:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>I valori visualizzati sono impostati per impostazione predefinita in Windows Server 2012 R2 e versioni successive. Per queste versioni di Windows, se le chiavi del registro di sistema sono assenti, non è necessario crearle.

### <a name="configuring-net-framework"></a>Configurazione di .NET Framework

Le seguenti chiavi del registro di sistema consentono di configurare .NET Framework per supportare la crittografia avanzata. Per altre informazioni sulla [configurazione di .NET Framework](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry), vedere qui.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-enable-tls-12"></a>Perché abilitare TLS 1,2?

TLS 1,2 è più sicuro rispetto ai protocolli di crittografia precedenti, ad esempio SSL 2,0, SSL 3,0, TLS 1,0 e TLS 1,1. I servizi di backup di Azure supportano già completamente TLS 1,2.

### <a name="what-determines-the-encryption-protocol-used"></a>Che cosa determina il protocollo di crittografia usato?

La versione del protocollo più elevata supportata dal client e dal server è negoziata per stabilire la conversazione crittografata. Per altre informazioni sul protocollo di handshake TLS, vedere [stabilire una sessione protetta usando TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Qual è l'effetto della mancata abilitazione di TLS 1,2?

Per una maggiore sicurezza dagli attacchi di downgrade del protocollo, backup di Azure sta iniziando a disabilitare le versioni TLS precedenti a 1,2 in modo graduale. Questa operazione fa parte di uno spostamento a lungo termine tra i servizi per impedire le connessioni legacy del protocollo e del pacchetto di crittografia. I servizi e i componenti di backup di Azure supportano completamente TLS 1,2. Tuttavia, le versioni di Windows prive di aggiornamenti necessari o alcune configurazioni personalizzate possono comunque impedire l'offerta di protocolli TLS 1,2. Ciò può causare errori, tra cui uno o più degli elementi seguenti:

- Le operazioni di backup e ripristino potrebbero non riuscire.
- La connessione dei componenti di backup ha esito negativo con errore 10054 (una connessione esistente è stata chiusa forzatamente dall'host remoto).
- I servizi correlati a backup di Azure non vengono arrestati o avviati come di consueto.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Protocollo Transport Layer Security](/windows/win32/secauthn/transport-layer-security-protocol)
- [Garanzia del supporto per TLS 1,2 tra sistemi operativi distribuiti](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Procedure consigliate per Transport Layer Security (TLS) con .NET Framework](/dotnet/framework/network-programming/tls)