# tuya2mqtt
Script Python pour interroger des appareils Tuya (locaux et cloud) via TinyTuya et publier leurs états en MQTT pour intégration dans Jeedom.

## Prérequis
- Compte Tuya IoT (https://iot.tuya.com/)
- TinyTuya installé (`pip install tinytuya`) - https://github.com/jasonacox/tinytuya/releases
- Serveur MQTT (ex. Mosquitto)
- Jeedom installé
- Python 3.x avec dépendances : `paho-mqtt`, `tinytuya`

## Installation
1. Clone le dépôt dans `/var/www/html/plugins/tuya2mqtt/` :
   ```bash
   git clone https://github.com/Pax24fr/tuya2mqtt.git /var/www/html/plugins/tuya2mqtt/
   ```
2. Ajuste les permissions :
   ```bash
   chown -R www-data:www-data /var/www/html/plugins/tuya2mqtt/
   chmod -R 755 /var/www/html/plugins/tuya2mqtt/
   ```
3. Configure `config.py` :
   - `MQTT_BROKER`, `MQTT_USER`, `MQTT_PASS`
   - `API_KEY`, `API_SECRET`, `API_REGION` (depuis Tuya IoT)
   - `DEVICE_IDS` avec IDs, IPs (optionnel), `local_key` (pour local), `power_code` (pour conso)
4. Lance le script :
   ```bash
   python3 /var/www/html/plugins/tuya2mqtt/main.py
   ```

## Fonctionnalités
- **Passerelle Tuya → MQTT** : Publie les états sur `tuya/<nom>`.
- **Contrôle** :
  - Local (avec IP) : toutes les 30s.
  - Cloud : 600s par défaut, 60s en charge, 180s en attente.
- **Consommation** : Calcule Wh pour appareils avec `power_code`.
- **Commandes MQTT** : Envoie `{"code": "<code>", "value": <valeur>}` sur `tuya/<nom>/cmd`.
- **Logs** : Dans `/var/www/html/log/tuya2mqtt.log` disponible dans Jeedom - Analyse - Logs.

## Exemples de commandes MQTT
- Allumer Clim :
  ```json
  {"code": "switch", "value": true}
  ```
- Régler température ClimAirton à 25°C :
  ```json
  {"code": "temp_set", "value": 25}
  ```
- Publier sur : `tuya/Clim/cmd`

## Fichiers
- `config.py` : Configuration MQTT, Tuya, appareils.
- `main.py` : Boucle principale et interrogations.
- `mqtt_handler.py` : Gestion MQTT et commandes.
- `energy_tracker.py` : Calcul et sauvegarde conso.
