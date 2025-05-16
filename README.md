// Configuration
let mqtt_local_topic = "shelly3em/local";
let mqtt_cloud_topic = "shelly3em/cloud";  // si cloud via MQTT
let cloud_http_url = "http://ton-serveur-cloud/endpoint";  // si cloud via HTTP POST

let interval_sec = 60; // Intervalle de publication (en secondes)
let cloud_via_http = true;  // true = HTTP POST, false = MQTT

// Fonction pour récupérer les données des 3 phases
function get_phases_data() {
  let data = {
    time: (new Date()).toISOString(),
    phase: [],
    total_power: 0
  };

  for (let i = 0; i < 3; i++) {
    let em = Shelly.getComponentStatus("em:data", { id: i });
    if (em !== null) {
      let p = {
        id: i+1,
        voltage: em.voltage,
        current: em.current,
        power: em.power,
        pf: em.pf,
        total: em.total_act_energy
      };
      data.phase.push(p);
      data.total_power += em.power;
    }
  }
  return data;
}

// Fonction de publication locale (MQTT)
function publish_local(data) {
  MQTT.publish(mqtt_local_topic, JSON.stringify(data), 0, false);
}

// Fonction de publication cloud
function publish_cloud(data) {
  if (cloud_via_http) {
    HTTP.request({
      url: cloud_http_url,
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(data),
      timeout: 5,
      success: function(res, body) {
        print("Cloud HTTP POST OK:", res);
      },
      error: function(err) {
        print("HTTP error:", err);
      }
    });
  } else {
    MQTT.publish(mqtt_cloud_topic, JSON.stringify(data), 0, false);
  }
}

// Boucle de mesure + publication
Timer.set(interval_sec * 1000, true, function () {
  let data = get_phases_data();
  publish_local(data);
  publish_cloud(data);
}, null);
