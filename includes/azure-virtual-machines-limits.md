Risorsa|Limite predefinito|Limite massimo
--|--|--
[Macchine virtuali](../documentation/services/virtual-machines/) per servizio cloud<sup>1</sup>|50|50
Endpoint di input per servizio cloud<sup>2</sup>|150|150

<sup>1</sup>Macchine virtuali create in Gestione dei servizi (anziché Gestione risorse) vengono automaticamente archiviate in un servizio cloud. È possibile aggiungere più macchine virtuali al servizio cloud per il bilanciamento del carico e disponibilità. Vedere [Come connettere le macchine virtuali con una rete virtuale o un servizio cloud](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Endpoint di input consentono le comunicazioni a una macchina virtuale non provenienti dal servizio cloud della macchina virtuale. Le macchine virtuali nello stesso servizio cloud o rete virtuale può comunicare automaticamente tra loro. Vedere: [Come configurare gli endpoint in una macchina virtuale](../virtual-machines/virtual-machines-set-up-endpoints.md)

<!---HONumber=August15_HO6-->