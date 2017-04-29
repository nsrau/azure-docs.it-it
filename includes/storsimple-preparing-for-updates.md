<!--author=jgerend last changed: 03/16/16-->

## <a name="preparing-for-updates"></a>Preparazione per gli aggiornamenti
È necessario effettuare le seguenti operazioni prima di analizzare e applicare l'aggiornamento:

1. Creare uno snapshot cloud dei dati del dispositivo.
2. Assicurarsi che il controller fisso di indirizzi IP sia instradabile e possa connettersi a Internet. Questi indirizzi IP fissi verranno usati per gestire gli aggiornamenti al dispositivo. È possibile effettuare una prova eseguendo il cmdlet seguente su ciascun controller dall'interfaccia di Windows PowerShell del dispositivo:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **Output di esempio di Test-Connection quando IP fissi non riescono a connettersi a Internet**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Dopo aver completato correttamente i controlli preliminari manuali, è possibile procedere all'analisi e installazione degli aggiornamenti.

