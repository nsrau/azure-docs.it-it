## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

* Per la disponibilità delle VM serie N, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/en-us/regions/services/).

* Le VM serie N possono essere distribuite solo nel modello di distribuzione Resource Manager.

* Le macchine virtuali serie N differiscono nel tipo di Archiviazione di Azure supportato per i dischi. Le VM serie NC e NV supportano solo dischi della macchina virtuale con archiviazione su disco di Azure di tipo Standard (HDD). Le VM serie NCv2, ND e NCv3 (anteprima) supportano solo dischi della macchina virtuale con archiviazione su disco di Azure di tipo Premium (SSD).

* Per distribuire numerose VM serie N, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

* Potrebbe essere necessario aumentare la quota di core (per area) nella sottoscrizione di Azure e la quota separata di core NC, NCv2, ND o NV. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../articles/azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

* Un'immagine VM che è possibile distribuire nelle VM serie N è la [macchina virtuale per l'analisi scientifica dei dati di Azure](../articles/machine-learning/data-science-virtual-machine/overview.md). La macchina virtuale per l'analisi scientifica dei dati preinstalla e configura numerosi strumenti comuni per l'analisi scientifica dei dati e l'apprendimento avanzato. Preinstalla anche i driver GPU NVIDIA Tesla.





