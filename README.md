### Descrizione esercizio:

Lo scopo di questo esercizio è quello di utilizzare Vagrant multinodo per creare 2 macchine virtuali (soufe1 e soufe2) con comunicazione su interfacce network locali.
Si dovrà scegliere tra la distribuzione Centos, Almalinux, RockyLinux e Oracle Linux.

Bisognerà creare un ruolo ansible denominato sou_podman, il quale dovrà: 

- Installare podman su soufe1 e soufe2
- Installare HaProxy su soufe1 tramite podman e utilizzare un template Ansible per haproxy.cfg ovvero il file di configurazione di HaProxy
- Installare Prometheus e Grafana tramite podman su soube2. Bisognerà utilizzare volumi ad-hoc per dati e file di configurazione. I file di configurazione grafana.ini e prometheus.yml occorre che siano gestiti tramite template Ansible. 

Per verificare il funzionamento del progetto, si dovrà raggiungere Grafana e Prometheus via http o https tramite HaProxy (bonus per https con creazione certificati anche self-signed).

HaProxy dovrà quindi operare come Reverse proxy per Prometheus e Grafana

### Tools utilizzati:

- Vagrant (https://docs.openshift.com/)
- Ansible (https://docs.ansible.com/)
- Ansible roles (https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
- Podman (https://docs.podman.io/en/latest/)
- Haproxy (https://docs.haproxy.org/)
- Prometheus (https://prometheus.io/docs/introduction/overview/)
- Grafana (https://grafana.com/docs/)

### Svolgimento:
Per lo svolgimento di questo esercizio è stato inizialmente creato un Vagrantfile che permettesse la creazione di Soufe1 e Soufe2. Sono stati attribuiti 2 indirizzi IP appartenenti alla stessa subnet e nello stesso file è stato predisposto il provisioning del playbook che verrà creato più avanti.

Poi è stato utilizzato il comando "ansible-galaxy init soupodman" per creare la struttura ad albero tipica dei roles. 
Dopo di ciò all' interno della cartella Tasks sono stati inseriti i vari comandi da eseguire: sono stati scritti utilizzati moduli affinché venisse installato Podman su entrambe le macchine virtuali, venissero creati i container Haproxy, Prometheus e Grafana sulle rispettive macchine e venissero creati i certificati che serviranno più avanti. 

Nella cartella Template sono stati creati i file Grafana.ini, Prometheus.conf.j2 e Haproxy.cfg.j2, che serviranno come file di configurazione per i 3 container. Nei task i file Grafana.ini e Prometheus.conf.j2 verranno richiamati tramite Template.

All' interno di haproxy.conf.j2 è stato specificato il percorso dei certificati ed è stato specificato come Haproxy debba andare a richiamare il container Prometheus sulla porta 9090 e Grafana sulla porta 3000.

I certificati sono stati creati tramite modulo Ansible, sono poi stati copiati tramite modulo all' interno di Soufe1 e da Soufe1 al container Haproxy tramite Volume. Anche il file Haproxy.conf.j2 è stato copiato sul container allo stesso modo.  

Dopo aver completato la scrittura delle task, è stato creato il playbook che permettesse di richiamare i tasks, infatti al suo interno verrà solo specificato il role soupodman che è stato utilizzato. 

Prima di lanciare il playbook e verificare la riuscita dell' esercizio, bisogna andare a modificare la tabella dns locale: si dovrà andare nel file /etc/hosts, andando ad aggiungere l' indirizzo IP di Haproxy seguito da foo.local e foobar.local. 

Infine si potranno effettuare le varie verifiche andando sul browser del nostro pc host, scrivendo sulla barra di ricerca: https://foobar.local o https://foo.local. In questo modo dal nostro pc verrà richiamato l' indirizzo IP di Haproxy, che a sua volta andrà a richiamare quello di Grafana o Prometheus sulle porte specificate nei file di configurazione e utilizzando i certificati autoprodotti.
