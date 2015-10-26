<properties 
	pageTitle="Servizi cloud di Azure - Definizione dei servizi e configurazione del servizio - Certificato XML" 
	description="Informazioni su come configurare un certificato nei file di definizione e configurazione del servizio." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015"
	ms.author="adegeo"/>



# Configurare la definizione del servizio e la configurazione per un certificato

Alle macchine virtuali che eseguono ruoli web o di lavoro possono essere associati certificati. Dopo aver caricato il certificato nel portale, è necessario configurare i file di definizione dei servizi (file csdef) e di configurazione dei servizi (file cscfg) per il certificato.

La macchina virtuale può accedere alla chiave privata del certificato dopo essere stata installata. Per questo motivo, è possibile restringere l'accesso ai processi con autorizzazioni elevate.

## Esempio di definizione del servizio

Ecco un esempio di certificato definito nella definizione del servizio.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### Autorizzazioni
Le autorizzazioni (attributo `permisionLevel`) possono essere impostate su uno dei seguenti valori:

| Valore di autorizzazione | Descrizione |
| ----------------  | ----------- |
| limitedOrElevated | **(Predefinito)** Tutti i processi di ruolo possono accedere alla chiave privata. |
| elevated | Solo i processi con autorizzazioni elevate possono accedere alla chiave privata.|

## Esempio di configurazione del servizio

Ecco un esempio di certificato definito nella configurazione del servizio.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**Si notino** gli attributi `name` corrispondenti.

## Passaggi successivi
Esaminare lo schema [XML di definizione dei servizi](https://msdn.microsoft.com/library/azure/ee758711.aspx) e lo schema [XML di configurazione del servizio](https://msdn.microsoft.com/library/azure/ee758710.aspx).

<!---HONumber=Oct15_HO3-->