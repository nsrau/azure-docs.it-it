##Impostazioni TCP per le VM di Azure

Le VM di Azure comunicano con la rete Internet pubblica tramite [NAT][nat] (Network Address Translation). I dispositivi NAT assegnano un indirizzo IP pubblico e una porta a una VM di Azure, consentendo a tale VM di stabilire un socket per la comunicazione con altri dispositivi. Se i pacchetti smettono di passare attraverso tale socket dopo un periodo specifico di tempo, il dispositivo NAT annulla il mapping e il socket può essere usato da altre macchine virtuali.

Si tratta di un comportamento NAT comune, che può causare problemi di comunicazione in applicazioni basate sul protocollo TCP che prevedono che un socket venga mantenuto oltre un periodo di timeout. Esistono due impostazioni del timeout di inattività da tenere in considerazione, per le sessioni che si trovano nello stato di *connessione stabilita*:

- In **ingresso** tramite [Azure load balancer][azure-lb-timeout]. Questo timeout ha come impostazione predefinita 4 minuti e può essere regolato fino a 30 minuti.
- In **uscita** tramite [SNAT][snat] (Source NAT). Questo timeout è impostato su 4 minuti e non può essere modificato.

Per garantire che le connessioni non vengano perse oltre il limite di timeout, assicurarsi che l'applicazione mantenga attiva la sessione oppure configurare il sistema operativo sottostante per questa operazione. Le impostazioni da usare sono diverse per i sistemi Linux e Windows, come illustrato di seguito.

Per [Linux][linux], è necessario modificare le variabili Kernel riportate di seguito. net.ipv4.tcp\_keepalive\_time = 120 net.ipv4.tcp\_keepalive\_intvl = 30 net.ipv4.tcp\_keepalive\_probes = 8
 
Per [Windows][windows], è necessario modificare i valori del registro riportati di seguito. KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


Le impostazioni precedenti assicurano che un pacchetto keep-alive venga inviato dopo 2 minuti (120 secondi) di tempo di inattività e quindi inviato ogni 30 secondi. Se 8 di questi pacchetti hanno esito negativo, la sessione viene annullata.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md