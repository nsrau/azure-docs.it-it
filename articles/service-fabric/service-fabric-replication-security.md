<properties
   pageTitle="Proteggere il traffico di replica dei servizi con stato in Azure Service Fabric"
   description="Quando è abilitata la replica, i servizi con stato replicano il relativo stato da una replica primaria a una secondaria. Tale traffico deve essere protetto da intrusioni e manomissioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# Proteggere il traffico di replica

Quando è abilitata la replica, i servizi con stato replicano lo stato tra le repliche. Questo articolo mostra come configurare la protezione per tale traffico.

Sono supportati due tipi di impostazioni di sicurezza:

- X509: usa il certificato X509 per proteggere il canale di comunicazione. Gli utenti devono applicare un certificato ACL alle chiavi private per consentire ai processi host del servizio/attore di usare le credenziali del certificato.
- Windows: usa la sicurezza di Windows (è necessario Active Directory) per proteggere il canale di comunicazione.

La sezione seguente mostra come configurare i due tipi di impostazione sopra indicati. La configurazione "CredentialType" determina quale tipo viene usato.

## CredentialType=X509

### Nomi delle configurazioni

|Nome|Osservazioni|
|----|-------|
|StoreLocation|Posizione dell'archivio per la ricerca del certificato: CurrentUser o LocalMachine|
|StoreName|Nome dell'archivio per la ricerca del certificato|
|FindType|Identifica il tipo di valore fornito dal parametro FindValue: FindBySubjectName o FindByThumbPrint|
|FindValue|Destinazione della ricerca del certificato|
|AllowedCommonNames|Elenco delimitato da virgole di nomi/nomi DNS comuni usati dai replicatori.|
|IssuerThumbprints|Elenco delimitato da virgole di identificazioni dell'autorità di certificazione. Quando specificato, viene verificato se il certificato in ingresso proviene da una delle voci dell'elenco. Viene sempre eseguita una convalida della catena.|
|RemoteCertThumbprints|Elenco delimitato da virgole di identificazioni del certificato usate dai replicatori.|
|ProtectionLevel|Indica la modalità di protezione dei dati: Sign, EncryptAndSign o None|

## CredentialType=Windows

### Nomi delle configurazioni

|Nome|Osservazioni|
|----|-------|
|ServicePrincipalName|Nome dell'entità servizio registrato per il servizio. Può essere vuoto se i processi host del servizo/attore vengono eseguiti come account di un computer, ad esempio NetworkService, LocalSystem e così via.|
|WindowsIdentities|Elenco delimitato da virgole di identità Windows per tutti i processi host del servizio/attore.
|ProtectionLevel|Indica la modalità di protezione dei dati: Sign, EncryptAndSign o None|

## Esempi

### Esempio 1: CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### Esempio 2: CredentialType=Windows
Questo frammento di codice mostra un esempio in cui tutti i processi host del servizo/attore vengono eseguiti come NetworkService o LocalSystem. ServicePrincipalName è vuoto.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### Esempio 3: CredentialType=Windows
Questo frammento di codice mostra un esempio in cui tutti i processi host del servizo/attore vengono eseguiti come account di un servizio gestito in base a gruppi con un valore di ServicePrincipalName valido.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO4-->