
---
title: Personalizzare le impostazioni del cluster di Service Fabric e dei criteri di aggiornamento dell'infrastruttura | Microsoft Docs
description: Questo articolo descrive le impostazioni dell'infrastruttura e i criteri di aggiornamento dell'infrastruttura che è possibile personalizzare.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2016
ms.author: chackdan

---
# Personalizzare le impostazioni del cluster Service Fabric e dei criteri di aggiornamento dell'infrastruttura
Questo documento illustra come personalizzare le varie impostazioni dell'infrastruttura e i criteri di aggiornamento della stessa per il cluster di Service Fabric. È possibile personalizzarli nel portale o mediante di modello di Resource Manager.

## Impostazioni di infrastruttura che è possibile personalizzare
Di seguito sono riportate le impostazioni di infrastruttura che è possibile personalizzare.

### Nome della sezione: Security
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ClusterProtectionLevel |Nessuno o EncryptAndSign |Nessuno (predefinito) per i cluster non protetti, EncryptAndSign per i cluster protetti. |

### Nome della sezione: Hosting
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tempo in secondi, il valore predefinito è 300 |Tempo massimo consentito per la registrazione di ServiceType nell'infrastruttura |
| ServiceTypeDisableFailureThreshold |Numero intero, il valore predefinito è 1 |Si tratta della soglia per il conteggio degli errori superata la quale a FailoverManager (FM) viene notificato di disabilitare il tipo di servizio sul nodo e di provare il posizionamento su un altro nodo. |
| ActivationRetryBackoffInterval |Tempo in secondi, il valore predefinito è 5 |Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
| ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |In ogni caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in ActivationMaxFailureCount. ActivationMaxRetryInterval specifica l'intervallo di tempo di attesa prima di un nuovo tentativo dopo ogni errore di attivazione |
| ActivationMaxFailureCount |Numero intero, il valore predefinito è 10 |Numero di volte che il sistema ritenta l'attivazione non riuscita prima di rinunciare |

### Nome della sezione: FailoverManager
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |Tempo in secondi, il valore predefinito è 10 |Determina la frequenza con cui FM controlla i nuovi report di carico |

### Nome della sezione: Federation
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| LeaseDuration |Tempo in secondi, il valore predefinito è 30 |Durata di un lease tra un nodo e gli elementi adiacenti. |
| LeaseDurationAcrossFaultDomain |Tempo in secondi, il valore predefinito è 30 |Durata di un lease tra un nodo e gli elementi adiacenti nei domini di errore. |

### Nome della sezione: ClusterManager
| **Parametro** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- |
| UpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza di polling sullo stato di aggiornamento dell'applicazione. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza dei controlli sullo stato di integrità durante gli aggiornamenti di un'applicazione monitorata |
| FabricUpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza di polling dello stato di aggiornamento dell'infrastruttura. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Frequenza dei controlli sullo stato di integrità durante un aggiornamento di un'infrastruttura monitorata |

## Passaggi successivi
Per ulteriori informazioni sulla gestione del cluster, leggere questi articoli:

[Aggiunta, rollover e rimozione di certificati dal cluster di Azure ](service-fabric-cluster-security-update-certs-azure.md)

<!---HONumber=AcomDC_0921_2016-->