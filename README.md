
Simply create a manual map. Insert the YAML code, name, and sensor information, and you're done.

type: custom:button-card

variables:
  plant_name: "Pflanze"
  soil: sensor.example_soil
  temp: sensor.example_temp
  lux: sensor.example_lux
  ec: sensor.example_ec

show_state: false
show_icon: false

name: |
  [[[
    const soil = Number(states[variables.soil].state);
    const ec = Number(states[variables.ec].state);

    let icon = "";
    if (soil < 30) icon = "<span class='iconRed iconBig'>🥀</span>";
    else if (soil < 40) icon = "<span class='iconOrange iconBig'>🌼</span>";
    else if (soil < 70) icon = "<span class='iconGreen iconBig'>🌸</span>";
    else icon = "<span class='iconBlue iconBig'>💧</span>";

    let status = "";
    if (soil < 30) status = "💧 Gießen empfohlen";
    else if (ec < 300) status = "⚡ Düngen empfohlen";
    else if (soil > 80) status = "🚨 Zu nass!";
    else if (ec > 1200) status = "🚨 Zu viel Dünger!";
    else status = "🟢 Alles OK";

    return icon + "<br><b>" + variables.plant_name + "</b><br><span class='statusText'>" + status + "</span>";
  ]]]

custom_fields:
  bars: |
    [[[
      const soil = Number(states[variables.soil].state);
      const temp = Number(states[variables.temp].state);
      const lux = Number(states[variables.lux].state);
      const ec = Number(states[variables.ec].state);

      const clamp = (v, max) => Math.max(0, Math.min(100, (v / max) * 100));

      function bar(label, value, max, color, unit) {
        return `
          <div class="row">
            <div class="label">${label}</div>
            <div class="bar-bg">
              <div class="bar-fill" style="width:${clamp(value,max)}%; background:${color};"></div>
            </div>
            <div class="val">${value}${unit}</div>
          </div>
        `;
      }

      return `
        ${bar("🌱 Boden", soil, 100, "#7CFC00", "%")}
        ${bar("🌡 Temp", temp, 40, "#4db8ff", "°C")}
        ${bar("☀️ Licht", lux, 10000, "#ffa64d", "lx")}
        ${bar("⚡ EC", ec, 2000, "#7CFC00", "µS")}
      `;
    ]]]

styles:
  grid:
    - grid-template-areas: '"n" "bars"'
    - grid-template-rows: min-content auto

  card:
    - border-radius: 22px
    - padding: 20px
    - background: rgba(255,255,255,0.10)
    - backdrop-filter: blur(12px)
    - border: 1px solid rgba(255,255,255,0.25)
    - box-shadow: 0 3px 8px rgba(0,0,0,0.30)
    - color: "#FFFFFF"
    - text-align: center

  name:
    - font-size: 18px
    - font-weight: bold

  custom_fields:
    bars:
      - margin-top: 15px
      - text-align: left

extra_styles: |
  .iconBig { font-size: 3em; }

  .statusText {
    display: block;
    margin-top: 5px;
    font-size: 14px;
    font-weight: 600;
    opacity: 0.9;
  }

  .iconRed { color: #ff4d4d; }
  .iconOrange { color: #ffa64d; }
  .iconGreen { color: #7CFC00; }
  .iconBlue { color: #4db8ff; }

  .row {
    display: grid;
    grid-template-columns: 90px 1fr 55px;
    align-items: center;
    margin-bottom: 8px;
    font-size: 14px;
  }

  .label { opacity: 0.8; }

  .val {
    text-align: right;
    font-weight: 600;
  }

  .bar-bg {
    height: 8px;
    background: rgba(255,255,255,0.2);
    border-radius: 10px;
    overflow: hidden;
    margin: 0 8px;
  }

  .bar-fill {
    height: 100%;
    border-radius: 10px;
    transition: width 0.4s ease;
  }
