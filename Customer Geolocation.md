<p align="center">
  <img width="420" height="120" alt="Dashboard Banner" src="https://github.com/user-attachments/assets/b6b12445-4a2b-4f34-92c4-7819e5c491cc" />
</p>

<h1 align="center">
  <img src="https://img.icons8.com/fluency/48/combo-chart.png" width="36" alt="analytics"/> 
  <span style="vertical-align:middle"> Customer Geolocation </span>
  <img src="https://img.icons8.com/fluency/48/speed.png" width="36" alt="performance"/>
</h1>


<!-- KPI badges (uniform) -->
<p align="center">
  <img src="https://img.shields.io/badge/Location-Lat:%2033.6844%20Long:%2073.0479-0ea5e9?style=for-the-badge&logo=google-maps" alt="Latitude and Longitude" /> &nbsp;
  <img src="https://img.shields.io/badge/Altitude-508m-0892ff?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxMCIgaGVpZ2h0PSIxMCI+PHBhdGggZmlsbD0iIzAwNjZGIiBkPSJNNSAwaDQuNXYxSDR2LjVBNSAzIDUgMCAwIDAgMCAwSDEuNUFNLTI0mcxMjExMjUyODEQX18NPSIvPjwvc3ZnPg==" alt="Altitude" /> &nbsp;
  <img src="https://img.shields.io/badge/Navigator-Active-06b6d4?style=for-the-badge&logo=google-earth" alt="Navigator" /> &nbsp;
  <img src="https://img.shields.io/badge/Distance%20Covered-12.5%20km-00c853?style=for-the-badge&logo=google-fit" alt="Distance Covered" /> &nbsp;
  <img src="https://img.shields.io/badge/Direction-North-ffc107?style=for-the-badge" alt="Direction" />
</p>

<p align="center">
  <b>Navigate, Explore & Track Your Locations Seamlessly</b>
</p>

<!-- Small visual row -->
<p align="center">
  <img src="https://img.icons8.com/fluency/48/marker.png" width="32" alt="map location"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/google-maps-new.png" width="32" alt="google map"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/map-pin.png" width="32" alt="map pin"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/street-view.png" width="32" alt="street view"/> &nbsp;
  <img src="https://img.icons8.com/color/48/globe--v1.png" width="32" alt="globe"/>
</p>


---

<p align="center">
  <b>Navigate, Explore & Track Your Locations Seamlessly</b>
</p>

<table align="center" border="1" cellspacing="0" cellpadding="8" style="border-collapse: collapse; width: 80%; text-align: left; font-family: Arial, sans-serif;">
  <thead>
    <tr style="background-color: #f2f2f2;">
      <th>Field Name</th>
      <th>Field Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>Geolocation</code></td>
      <td>Geolocation</td>
      <td>Stores the geolocation (latitude and longitude) of the customer.</td>
    </tr>
    <tr>
      <td><code>Longitude</code></td>
      <td>Float</td>
      <td>Longitude value representing the geographical coordinate.</td>
    </tr>
    <tr>
      <td><code>Latitude</code></td>
      <td>Float</td>
      <td>Latitude value representing the geographical coordinate.</td>
    </tr>
    <tr>
      <td><code>Map Url</code></td>
      <td>Data</td>
      <td>URL for opening the customer's location on an online map (e.g., Google Maps).</td>
    </tr>
    <tr>
      <td><code>Current Geolocation Address</code></td>
      <td>Small Text</td>
      <td>Displays the human-readable address of the current geolocation.</td>
    </tr>
    <tr>
      <td><code>Mobile</code></td>
      <td>Data</td>
      <td>Mobile phone number of the customer.</td>
    </tr>
    <tr>
      <td><code>Email</code></td>
      <td>Data</td>
      <td>Email address of the customer.</td>
    </tr>
  </tbody>
</table>



---



## <img src="https://img.icons8.com/color/48/000000/javascript--v1.png" width="35"/> JavaScript Code (Client Script)

<details>
<summary><img src="https://img.icons8.com/color/48/000000/code-file.png" width="22"/> Copy JavaScript</summary>

```javascript

/**
 * Customer Geolocation Client Script (ERPNext/Frappe v15) - UI-only
 *
 * v4 FIX (FOCUS ON REFRESH/REOPEN):
 * - Uses the same proven logic from your original working script:
 *   render_saved_location_when_map_ready(frm) + attach_tab_render_hook(frm)
 * - This forces the Geolocation widget to re-render after refresh / tab switch / lat-lng change.
 *
 * Buttons:
 * - Fetch Geolocation
 * - Fetch from Longitude & Latitude
 * - Fetch from Url
 * - OS Map / OS Map Nav
 * - Google Map / Google Nav
 * - Save Address
 * - Save Contact
 *
 * Required fields:
 * custom_latitude, custom_longitude, custom_geolocation, custom_current_geolocation_address, custom_map_url
 *
 * Optional Server Script (API): parse_map_url (only parses URLs that already contain coords)
 */

const BTN_STYLE = {
  fetch:        { bg: "#6f42c1", text: "#ffffff" },
  osm_open:     { bg: "#198754", text: "#ffffff" },
  osm_nav:      { bg: "#157347", text: "#ffffff" },
  g_open:       { bg: "#0d6efd", text: "#ffffff" },
  g_nav:        { bg: "#0b5ed7", text: "#ffffff" },
  save_addr:    { bg: "#fd7e14", text: "#ffffff" },
  save_contact: { bg: "#6c757d", text: "#ffffff" },
};

function style_btn($btn, cfg) {
  if (!cfg) return;
  $btn.css({ background: cfg.bg, color: cfg.text, borderColor: cfg.bg });
}

function _num(v) {
  const n = parseFloat(v);
  return Number.isFinite(n) ? n : null;
}

function point_geojson(lat, lng) {
  return {
    type: "FeatureCollection",
    features: [{
      type: "Feature",
      properties: {},
      geometry: { type: "Point", coordinates: [parseFloat(lng), parseFloat(lat)] }
    }]
  };
}

function has_saved_location(frm) {
  return _num(frm.doc.custom_latitude) != null && _num(frm.doc.custom_longitude) != null;
}

function osm_pin_url(lat, lng) {
  return `https://www.openstreetmap.org/?mlat=${encodeURIComponent(lat)}&mlon=${encodeURIComponent(lng)}#map=18/${encodeURIComponent(lat)}/${encodeURIComponent(lng)}`;
}
function osm_nav_url(lat, lng) {
  return `https://www.openstreetmap.org/directions?engine=fossgis_osrm_car&route=${encodeURIComponent(lat)}%2C${encodeURIComponent(lng)}`;
}
function google_pin_url(lat, lng) {
  return `https://www.google.com/maps?q=${encodeURIComponent(lat)},${encodeURIComponent(lng)}`;
}
function google_nav_url(lat, lng) {
  return `https://www.google.com/maps/dir/?api=1&destination=${encodeURIComponent(lat)},${encodeURIComponent(lng)}`;
}

/***********************
 * Leaflet loader (dialog)
 ***********************/
function ensure_leaflet_loaded() {
  return new Promise((resolve, reject) => {
    if (window.L) return resolve();

    const js = "/assets/frappe/js/lib/leaflet/leaflet.js";
    const css = "/assets/frappe/js/lib/leaflet/leaflet.css";

    frappe.require([js, css], () => {
      if (window.L) resolve();
      else reject(new Error("Leaflet not loaded. Check asset paths."));
    });
  });
}

/***********************
 * ✅ PROVEN FOCUS LOGIC (from your original working script)
 ***********************/
function render_saved_location_when_map_ready(frm) {
  const lat = frm.doc.custom_latitude;
  const lng = frm.doc.custom_longitude;
  if (!lat || !lng) return;

  const f = frm.fields_dict.custom_geolocation;
  if (!f || !f.$wrapper) return;

  let tries = 0;
  const max_tries = 25;

  const timer = setInterval(() => {
    tries++;

    const visible = f.$wrapper.is(":visible");
    if (!visible && tries < max_tries) return;

    try {
      const geo_str = JSON.stringify(point_geojson(lat, lng));

      // IMPORTANT: don't use set_value here (it can fail to re-center and marks dirty)
      frm.doc.custom_geolocation = geo_str;

      frm.refresh_field("custom_geolocation");
      if (typeof f.refresh === "function") f.refresh();

      clearInterval(timer);
    } catch (e) {
      if (tries >= max_tries) {
        clearInterval(timer);
        console.error("Geolocation render failed:", e);
      }
    }

    if (tries >= max_tries) clearInterval(timer);
  }, 250);
}

function attach_tab_render_hook(frm) {
  if (frm.__geo_tab_hooked) return;
  frm.__geo_tab_hooked = true;

  $(frm.page.wrapper).on("shown.bs.tab", "a[data-bs-toggle='tab'], a[data-toggle='tab']", function () {
    render_saved_location_when_map_ready(frm);
  });
}

/***********************
 * Hook
 ***********************/
frappe.ui.form.on("Customer", {
  refresh(frm) {
    inject_action_bar_above_map(frm);
    render_saved_location_when_map_ready(frm);
    attach_tab_render_hook(frm);
  },

  onload_post_render(frm) {
    render_saved_location_when_map_ready(frm);
  },

  custom_latitude(frm) { render_saved_location_when_map_ready(frm); },
  custom_longitude(frm) { render_saved_location_when_map_ready(frm); },
});

/***********************
 * Action Bar
 ***********************/
function inject_action_bar_above_map(frm) {
  const f = frm.fields_dict.custom_geolocation;
  if (!f || !f.$wrapper) return;
  if (f.$wrapper.find(".geo-action-bar").length) return;

  const $bar = $(`<div class="geo-action-bar mb-2"
      style="display:flex; gap:8px; flex-wrap:wrap; align-items:center;"></div>`);

  const $fetch      = $(`<button class="btn btn-sm">Fetch Geolocation</button>`);
  const $fromLatLng = $(`<button class="btn btn-sm">Fetch from Longitude & Latitude</button>`);
  const $fromUrl    = $(`<button class="btn btn-sm">Fetch from Url</button>`);

  const $osmOpen    = $(`<button class="btn btn-sm">OS Map</button>`);
  const $osmNav     = $(`<button class="btn btn-sm">OS Map Nav</button>`);
  const $gOpen      = $(`<button class="btn btn-sm">Google Map</button>`);
  const $gNav       = $(`<button class="btn btn-sm">Google Nav</button>`);

  const $saveAddr   = $(`<button class="btn btn-sm">Save Address</button>`);
  const $saveCont   = $(`<button class="btn btn-sm">Save Contact</button>`);

  style_btn($fetch, BTN_STYLE.fetch);
  style_btn($fromLatLng, BTN_STYLE.fetch);
  style_btn($fromUrl, BTN_STYLE.g_open);

  style_btn($osmOpen, BTN_STYLE.osm_open);
  style_btn($osmNav, BTN_STYLE.osm_nav);
  style_btn($gOpen, BTN_STYLE.g_open);
  style_btn($gNav, BTN_STYLE.g_nav);

  style_btn($saveAddr, BTN_STYLE.save_addr);
  style_btn($saveCont, BTN_STYLE.save_contact);

  $fetch.on("click", async () => fetch_geolocation(frm));
  $fromLatLng.on("click", async () => fetch_from_current_latlng(frm));
  $fromUrl.on("click", async () => fetch_from_url_dialog(frm));

  $osmOpen.on("click", () => {
    if (!has_saved_location(frm)) return frappe.msgprint("No saved Lat/Lng.");
    window.open(osm_pin_url(frm.doc.custom_latitude, frm.doc.custom_longitude), "_blank");
  });
  $osmNav.on("click", () => {
    if (!has_saved_location(frm)) return frappe.msgprint("No saved Lat/Lng.");
    window.open(osm_nav_url(frm.doc.custom_latitude, frm.doc.custom_longitude), "_blank");
  });
  $gOpen.on("click", () => {
    if (!has_saved_location(frm)) return frappe.msgprint("No saved Lat/Lng.");
    window.open(google_pin_url(frm.doc.custom_latitude, frm.doc.custom_longitude), "_blank");
  });
  $gNav.on("click", () => {
    if (!has_saved_location(frm)) return frappe.msgprint("No saved Lat/Lng.");
    window.open(google_nav_url(frm.doc.custom_latitude, frm.doc.custom_longitude), "_blank");
  });

  $saveAddr.on("click", async () => save_gps_address_to_customer_address(frm));
  $saveCont.on("click", async () => sync_mobile_email_to_address_and_contact(frm));

  $bar.append($fetch, $fromLatLng, $fromUrl, $osmOpen, $osmNav, $gOpen, $gNav, $saveAddr, $saveCont);
  f.$wrapper.prepend($bar);
}

/***********************
 * 1) Fetch Geolocation
 ***********************/
async function fetch_geolocation(frm) {
  if (!navigator.geolocation) return frappe.msgprint("Geolocation not supported by this browser.");
  frappe.show_alert({ message: "Fetching location...", indicator: "blue" });

  return new Promise((resolve) => {
    navigator.geolocation.getCurrentPosition(async (pos) => {
      await set_lat_lng_and_fetch_address(frm, pos.coords.latitude, pos.coords.longitude);
      frappe.show_alert({ message: "Location saved", indicator: "green" });
      resolve();
    }, (err) => {
      frappe.msgprint("Error getting location: " + err.message);
      resolve();
    }, { enableHighAccuracy: true, timeout: 15000 });
  });
}

/***********************
 * 2) Fetch from Lat/Lng
 ***********************/
async function fetch_from_current_latlng(frm) {
  const lat = _num(frm.doc.custom_latitude);
  const lng = _num(frm.doc.custom_longitude);
  if (lat == null || lng == null) return frappe.msgprint("Please enter valid Latitude and Longitude first.");
  await set_lat_lng_and_fetch_address(frm, lat, lng);
}

/***********************
 * 3) Fetch from URL (Dialog + Leaflet map)
 ***********************/
async function fetch_from_url_dialog(frm) {
  const rawUrl = (frm.doc.custom_map_url || "").trim();
  if (!rawUrl) return frappe.msgprint("Please paste a map link in custom_map_url first.");

  const dlg = await open_url_map_dialog(frm, rawUrl);

  // Optional: if parse_map_url exists and URL contains coords, it will auto-center
  try {
    const r = await frappe.call({ method: "parse_map_url", args: { url: rawUrl } });
    if (r && r.message && r.message.ok) {
      const lat = _num(r.message.lat);
      const lng = _num(r.message.lng);
      if (lat != null && lng != null) {
        dlg.set_marker(lat, lng);
        await set_lat_lng_and_fetch_address(frm, lat, lng);
        return;
      }
    }
  } catch (e) {}

  if (/maps\.app\.goo\.gl/i.test(rawUrl)) {
    window.open(rawUrl, "_blank");
    frappe.msgprint("Short link opened in new tab. Now click the same point on the map and press 'Use This Location'.");
  } else {
    frappe.msgprint("URL did not contain coordinates. Please click on map and press 'Use This Location'.");
  }
}

async function open_url_map_dialog(frm, url) {
  await ensure_leaflet_loaded();
  const map_id = "geo_picker_" + frappe.utils.get_random(10);

  const d = new frappe.ui.Dialog({
    title: "Fetch from Url",
    fields: [
      { fieldtype: "HTML", fieldname: "info" },
      { fieldtype: "HTML", fieldname: "map_html" },
      { fieldtype: "Float", fieldname: "lat", label: "Latitude" },
      { fieldtype: "Float", fieldname: "lng", label: "Longitude" },
      { fieldtype: "Button", fieldname: "open_url", label: "Open URL in New Tab" },
    ],
    primary_action_label: "Use This Location",
    primary_action: async (v) => {
      const lat = _num(v.lat);
      const lng = _num(v.lng);
      if (lat == null || lng == null) return;
      await set_lat_lng_and_fetch_address(frm, lat, lng);
      d.hide();
    }
  });

  d.show();

  d.fields_dict.info.$wrapper.html(`
    <div style="margin-bottom:8px;">
      <div style="font-size:12px; opacity:.7;">URL</div>
      <div class="small text-muted" style="word-break:break-all;">${frappe.utils.escape_html(url)}</div>
      <div style="font-size:12px; opacity:.6; margin-top:6px;">Google cannot be embedded in iframe. Use internal map below.</div>
    </div>
  `);
  d.fields_dict.open_url.input.onclick = () => window.open(url, "_blank");

  d.fields_dict.map_html.$wrapper.html(
    `<div id="${map_id}" style="height:420px; border:1px solid #ddd; border-radius:8px;"></div>`
  );

  await new Promise((res) => setTimeout(res, 350));

  const initLat = _num(frm.doc.custom_latitude);
  const initLng = _num(frm.doc.custom_longitude);
  const startLat = (initLat != null && initLng != null) ? initLat : 31.5204;
  const startLng = (initLat != null && initLng != null) ? initLng : 74.3587;

  const map = L.map(map_id).setView([startLat, startLng], 14);
  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", { maxZoom: 19 }).addTo(map);

  let marker = L.marker([startLat, startLng], { draggable: true }).addTo(map);

  function sync(lat, lng) {
    d.set_value("lat", lat);
    d.set_value("lng", lng);
  }
  sync(startLat, startLng);

  map.on("click", (e) => {
    marker.setLatLng([e.latlng.lat, e.latlng.lng]);
    sync(e.latlng.lat, e.latlng.lng);
  });
  marker.on("dragend", () => {
    const p = marker.getLatLng();
    sync(p.lat, p.lng);
  });

  setTimeout(() => map.invalidateSize(), 200);

  d.set_marker = (lat, lng) => {
    marker.setLatLng([lat, lng]);
    map.setView([lat, lng], 16);
    sync(lat, lng);
    setTimeout(() => map.invalidateSize(), 50);
  };

  return d;
}

/***********************
 * Save lat/lng + geolocation + reverse geocode
 ***********************/
async function set_lat_lng_and_fetch_address(frm, lat, lng) {
  await frm.set_value("custom_latitude", lat);
  await frm.set_value("custom_longitude", lng);
  await frm.set_value("custom_geolocation", JSON.stringify(point_geojson(lat, lng)));

  frm.refresh_field("custom_latitude");
  frm.refresh_field("custom_longitude");
  frm.refresh_field("custom_geolocation");

  frm.dirty();
  await frm.save();

  await fetch_and_set_address(frm, lat, lng);

  // ensure focus
  render_saved_location_when_map_ready(frm);
}

async function fetch_and_set_address(frm, lat, lng) {
  try {
    const url = `https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=${encodeURIComponent(lat)}&lon=${encodeURIComponent(lng)}`;
    const r = await fetch(url, { headers: { "Accept": "application/json" } });
    const data = await r.json();

    await frm.set_value("custom_current_geolocation_address", data.display_name || "");
    frm.refresh_field("custom_current_geolocation_address");

    frm.dirty();
    await frm.save();
  } catch (e) {
    frappe.msgprint("Failed to fetch address: " + e.message);
  }
}

/***********************
 * Save Address / Save Contact placeholders
 * (Keep your existing logic if you already have it)
 ***********************/

/***********************
 * Save Address / Save Contact (restored)
 ***********************/

function parse_display_address(display_name) {
  if (!display_name) return null;

  const parts = display_name.split(",").map(s => s.trim()).filter(Boolean);
  if (parts.length < 5) {
    return {
      address_line1: display_name,
      city: "Unknown",
      state: "",
      pincode: "00000",
      country: "Pakistan"
    };
  }

  const country = parts[parts.length - 1] || "Pakistan";
  const pincode = parts[parts.length - 2] || "00000";
  const state = parts[parts.length - 3] || "";
  const city = parts[parts.length - 4] || "Unknown";
  const address_line1 = parts.slice(0, parts.length - 4).join(", ").trim() || display_name;

  return { address_line1, city, state, pincode, country };
}

async function save_gps_address_to_customer_address(frm) {
  const display_addr = frm.doc.custom_current_geolocation_address;
  if (!display_addr) {
    frappe.msgprint("No GPS address. Click Fetch first.");
    return;
  }

  const parsed = frm.__geo_parts || parse_display_address(display_addr) || {};
  const address_line1 = parsed.address_line1 || display_addr;
  const city = parsed.city || "Unknown";
  const state = parsed.state || "";
  const country = parsed.country || "Pakistan";
  const pincode = parsed.pincode || "00000";

  const line2 = "";

  // Update existing primary address
  if (frm.doc.customer_primary_address) {
    await frappe.call({
      method: "frappe.client.set_value",
      args: {
        doctype: "Address",
        name: frm.doc.customer_primary_address,
        fieldname: {
          address_title: frm.doc.customer_name || frm.doc.name,
          address_line1: address_line1,
          address_line2: line2,
          city: city,
          state: state,
          country: country,
          pincode: pincode
        }
      }
    });
    frappe.show_alert({ message: "Primary Address updated", indicator: "green" });
    return;
  }

  // Create new Address (mandatory fields)
  const new_address = {
    doctype: "Address",
    address_title: frm.doc.customer_name || frm.doc.name,
    address_type: "Billing",
    address_line1: address_line1,
    address_line2: line2,
    city: city,
    state: state,
    country: country,
    pincode: pincode,
    links: [{ link_doctype: "Customer", link_name: frm.doc.name }]
  };

  const r = await frappe.call({
    method: "frappe.client.insert",
    args: { doc: new_address }
  });

  const created_address_name = r.message?.name;
  if (!created_address_name) {
    frappe.msgprint("Address created but name not returned.");
    return;
  }

  await frm.set_value("customer_primary_address", created_address_name);
  frm.dirty();
  await frm.save();

  frappe.show_alert({ message: "New Address created + set Primary", indicator: "green" });
}

async function sync_mobile_email_to_address_and_contact(frm) {
  const mobile = ((frm.doc.custom_mobile || frm.doc.mobile_no || "") + "").trim();
  const email = ((frm.doc.custom_email || frm.doc.email_id || "") + "").trim();

  if (!frm.doc.name) return;

  if (!mobile && !email) {
    frappe.msgprint("Enter Mobile or Email first.");
    return;
  }

  // Update Address.phone / Address.email_id if primary address exists
  if (frm.doc.customer_primary_address) {
    const upd = {};
    if (mobile) upd.phone = mobile;
    if (email) upd.email_id = email;

    await frappe.call({
      method: "frappe.client.set_value",
      args: { doctype: "Address", name: frm.doc.customer_primary_address, fieldname: upd }
    });
  }

  // Update/Create Contact (phone_nos child table)
  let contact_name = frm.doc.customer_primary_contact;

  if (!contact_name) {
    const new_contact = {
      doctype: "Contact",
      first_name: frm.doc.customer_name || frm.doc.name,
      links: [{ link_doctype: "Customer", link_name: frm.doc.name }]
    };

    if (mobile) {
      new_contact.phone_nos = [{ phone: mobile, is_primary_phone: 1 }];
    }

    const ins = await frappe.call({
      method: "frappe.client.insert",
      args: { doc: new_contact }
    });

    contact_name = ins.message?.name;

    if (contact_name) {
      await frm.set_value("customer_primary_contact", contact_name);
      frm.dirty();
      await frm.save();
    }
  } else {
    const r = await frappe.call({
      method: "frappe.client.get",
      args: { doctype: "Contact", name: contact_name }
    });

    const contact = r.message;
    if (!contact) return frappe.msgprint("Could not load Primary Contact.");

    if (mobile) {
      contact.phone_nos = contact.phone_nos || [];
      let row = contact.phone_nos.find(d => (d.phone || "").trim() === mobile);

      if (!row) contact.phone_nos.push({ phone: mobile, is_primary_phone: 1 });

      contact.phone_nos.forEach(d => {
        d.is_primary_phone = ((d.phone || "").trim() === mobile) ? 1 : 0;
      });
    }

    await frappe.call({ method: "frappe.client.save", args: { doc: contact } });
  }

  frappe.show_alert({ message: "Contact + Address updated", indicator: "green" });
}


```
</details>


> **Note:**  
> Copy and use the JavaScript snippet above.

---




## <img src="https://img.icons8.com/color/48/000000/javascript--v1.png" width="35"/> JavaScript Code (Client Script) For Sales Order

<details>
<summary><img src="https://img.icons8.com/color/48/000000/code-file.png" width="22"/> Copy JavaScript</summary>

```javascript
frappe.ui.form.on('Sales Order', {
  refresh(frm) {
    // Standard button
    frm.add_custom_button(__('GPS Address'), async () => {
      await set_shipping_from_customer_gps(frm);
    });

    // Light-blue Google button (always visible in header area)
    inject_google_open_button(frm);
  }
});

/* -------------------- UI: Light Blue Google button -------------------- */

function inject_google_open_button(frm) {
  if (frm.__google_btn_added) return;
  frm.__google_btn_added = true;

  const $btn = $(
    `<button class="btn btn-sm btn-google-open" style="
        background:#5bc0de; border-color:#46b8da; color:#fff;
        margin-left:8px;">
        Google Map
     </button>`
  );

  $btn.on("click", async () => {
    await open_shipping_location_google(frm);
  });

  // Put into page actions (top right area)
  // Works on desktop; on mobile it still shows near top in most cases
  frm.page.btn_secondary && frm.page.btn_secondary.after($btn);
}

/* -------------------- Parse display address rule -------------------- */

function parse_display_address(display_name) {
  if (!display_name) return null;

  const parts = display_name.split(",").map(s => s.trim()).filter(Boolean);
  if (parts.length < 5) {
    return {
      address_line1: display_name,
      city: "Unknown",
      state: "",
      pincode: "00000",
      country: "Pakistan"
    };
  }

  const country = parts[parts.length - 1] || "Pakistan";
  const pincode = parts[parts.length - 2] || "00000";
  const state = parts[parts.length - 3] || "";
  const city = parts[parts.length - 4] || "Unknown";
  const address_line1 = parts.slice(0, parts.length - 4).join(", ").trim() || display_name;

  return { address_line1, city, state, pincode, country };
}

/* -------------------- Core: Set shipping from Customer GPS -------------------- */

async function set_shipping_from_customer_gps(frm) {
  const customer = frm.doc.customer;
  if (!customer) {
    frappe.msgprint("Please select Customer first.");
    return;
  }

  const r = await frappe.db.get_value(
    "Customer",
    customer,
    ["customer_primary_address", "custom_current_geolocation_address", "customer_name"]
  );

  const primary_address = r.message?.customer_primary_address;
  const gps_display_address = r.message?.custom_current_geolocation_address;
  const customer_name = r.message?.customer_name || customer;

  if (primary_address) {
    await apply_address_to_sales_order(frm, primary_address);
    frappe.show_alert({ message: "Shipping Address set from Customer Primary Address", indicator: "green" });
    return;
  }

  if (!gps_display_address) {
    frappe.msgprint("Customer has no GPS address saved. Open Customer → Fetch Geolocation first.");
    return;
  }

  const parsed = parse_display_address(gps_display_address);

  const new_address = {
    doctype: "Address",
    address_title: customer_name,
    address_type: "Shipping",
    address_line1: parsed.address_line1,
    address_line2: "",
    city: parsed.city,
    state: parsed.state,
    country: parsed.country,
    pincode: parsed.pincode,
    links: [{ link_doctype: "Customer", link_name: customer }]
  };

  const ins = await frappe.call({
    method: "frappe.client.insert",
    args: { doc: new_address }
  });

  const address_name = ins.message?.name;
  if (!address_name) {
    frappe.msgprint("Address created but name not returned. Check logs.");
    return;
  }

  // Best effort: set Customer Primary Address
  try {
    await frappe.db.set_value("Customer", customer, "customer_primary_address", address_name);
  } catch (e) {
    console.warn("Could not set Customer Primary Address:", e);
  }

  await apply_address_to_sales_order(frm, address_name);
  frappe.show_alert({ message: "Shipping Address created from Customer GPS and set on Sales Order", indicator: "green" });
}

/* -------------------- Apply address to Sales Order -------------------- */

async function apply_address_to_sales_order(frm, address_name) {
  await frm.set_value("shipping_address_name", address_name);
  await frm.set_value("customer_address", address_name);

  frm.refresh_fields();
  frm.dirty();
  await frm.save();
}

/* -------------------- Google: Open Shipping Location -------------------- */

async function open_shipping_location_google(frm) {
  // Prefer shipping_address_name; fallback to customer_address
  const addr_name = frm.doc.shipping_address_name || frm.doc.customer_address;

  if (!addr_name) {
    frappe.msgprint("No Shipping Address set. Click 'Set Shipping Address from Customer GPS' first.");
    return;
  }

  // Read address fields and make a query string
  const r = await frappe.db.get_value(
    "Address",
    addr_name,
    ["address_line1", "address_line2", "city", "state", "pincode", "country"]
  );

  const a = r.message || {};
  const q = [
    a.address_line1, a.address_line2, a.city, a.state, a.pincode, a.country
  ].filter(Boolean).join(", ");

  if (!q) {
    frappe.msgprint("Shipping Address record is empty.");
    return;
  }

  const url = `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(q)}`;
  window.open(url, "_blank");
}

```
</details>


> **Note:**  
> Copy and use the JavaScript snippet above.

---









---


## <img src="https://img.icons8.com/color/48/000000/python--v1.png" width="35"/> Python Server Script

<details>
<summary><img src="https://img.icons8.com/color/48/000000/code-file.png" width="22"/> Copy Python</summary>

```python
# Name: Customer Geolocation
# Script Type: API
# API Method: parse_map_url


def to_float(x):
    try:
        return float(x)
    except Exception:
        return None

def split_lat_lng(s):
    if not s or "," not in s:
        return None
    parts = s.split(",", 1)
    if len(parts) < 2:
        return None
    lat = to_float((parts[0] or "").strip())
    lng = to_float((parts[1] or "").strip())
    if lat is None or lng is None:
        return None
    return {"lat": lat, "lng": lng}

def extract_from_place(url):
    key = "/place/"
    i = url.find(key)
    if i < 0:
        return None
    s = url[i + len(key):]
    for stop in ["/", "?", "&"]:
        j = s.find(stop)
        if j >= 0:
            s = s[:j]
            break
    return split_lat_lng(s)

def extract_from_at(url):
    key = "/@"
    i = url.find(key)
    if i < 0:
        return None
    s = url[i + len(key):]
    parts = s.split(",")
    if len(parts) < 2:
        return None
    return split_lat_lng(parts[0].strip() + "," + parts[1].strip())

def extract_from_q(url):
    key = "q="
    i = url.find(key)
    if i < 0:
        return None
    s = url[i + len(key):]
    j = s.find("&")
    if j >= 0:
        s = s[:j]
    return split_lat_lng(s)

def extract_from_3d4d(url):
    i = url.find("!3d")
    j = url.find("!4d")
    if i < 0 or j < 0 or j < i:
        return None

    a = url[i + 3:j]
    k = url.find("!", j + 3)
    b = url[j + 3:] if k < 0 else url[j + 3:k]

    lat = to_float((a or "").strip())
    lng = to_float((b or "").strip())
    if lat is None or lng is None:
        return None
    return {"lat": lat, "lng": lng}

url = (frappe.form_dict.get("url") or "").strip()
if not url:
    frappe.throw("url is required")

result = extract_from_place(url)
if not result:
    result = extract_from_at(url)
if not result:
    result = extract_from_q(url)
if not result:
    result = extract_from_3d4d(url)

lat = None
lng = None
if result:
    lat = result.get("lat")
    lng = result.get("lng")

frappe.response["message"] = {
    "ok": True,
    "final_url": url,
    "lat": lat,
    "lng": lng
}

```
</details>

> **Note:**  
> Copy and Paste..............

---

##### Contact
<p align="center">
  <img src="https://github.com/ERPNEXT-PAKISTAN.png" width="96" height="96" alt="Taimoor"
       style="border-radius:50%; object-fit:cover; display:block; border:3px solid rgba(255,255,255,0.95); box-shadow:0 6px 18px rgba(0,0,0,0.25);" />
</p>

<h2 align="center">Taimoor</h2>
<p align="center"><em>ERPNext | Integrations & Automation</em></p>

<p align="center">
  <!-- Phone / WhatsApp -->
  <a href="https://wa.me/923009808900" title="WhatsApp" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/whatsapp.png" width="28" alt="WhatsApp"/> 
  </a>
  &nbsp;
  <a href="tel:+923009808900" title="Call">
    <img src="https://img.icons8.com/fluency/48/phone.png" width="28" alt="Phone"/>
  </a>
  &nbsp;&nbsp;
  <!-- Email -->
  <a href="mailto:taimoor986@gmail.com" title="Email">
    <img src="https://img.icons8.com/fluency/48/new-post.png" width="28" alt="Email"/>
  </a>
  &nbsp;&nbsp;
  <!-- LinkedIn -->
  <a href="https://www.linkedin.com/in/taimoor-khan-45b548137" title="LinkedIn" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/linkedin.png" width="28" alt="LinkedIn"/>
  </a>
  &nbsp;&nbsp;
  <!-- GitHub -->
  <a href="https://github.com/ERPNEXT-PAKISTAN" title="GitHub" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/github.png" width="28" alt="GitHub"/>
  </a>
  &nbsp;&nbsp;
  <!-- YouTube -->
  <a href="https://www.youtube.com/@ERPNEXT-PAKISTAN" title="YouTube" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/youtube-play.png" width="28" alt="YouTube"/>
  </a>
</p>


<p align="center">
  <b>Contact</b><br>
  📞 +92 300 9808900 &nbsp;|&nbsp; ✉️ <a href="mailto:taimoor986@gmail.com">taimoor986@gmail.com</a>
</p>

<p align="center">
  <a href="https://www.linkedin.com/in/taimoor-khan-45b548137" target="_blank">LinkedIn</a> • 
  <a href="https://github.com/ERPNEXT-PAKISTAN" target="_blank">GitHub</a> • 
  <a href="https://www.youtube.com/@ERPNEXT-PAKISTAN" target="_blank">YouTube</a> • 
  <a href="https://wa.me/923009808900" target="_blank">WhatsApp</a>
</p>

---

> ✨ **Tip:**  
> You can copy, share, or export this contact card using the icons above.
>
> <small>
> - Click the phone/WhatsApp icon to message or call.  
> - Click the mail icon to open your email client.  
> - Use the social icons to visit profiles and channels.
> </small>

<details>
  <summary><img src="https://img.icons8.com/fluency/48/copy.png" width="18" alt="Copy"/> Copy vCard</summary>

```vcf
BEGIN:VCARD
VERSION:3.0
FN:Taimoor
TEL;TYPE=CELL:+923009808900
EMAIL:taimoor986@gmail.com
URL;TYPE=LinkedIn:https://www.linkedin.com/in/taimoor-khan-45b548137
URL;TYPE=GitHub:https://github.com/ERPNEXT-PAKISTAN
URL;TYPE=YouTube:https://www.youtube.com/@ERPNEXT-PAKISTAN
END:VCARD
```
</details>

---

<!-- Action buttons (links) -->
<p align="center">
  <a href="#reports" style="text-decoration:none">
    <img src="https://img.shields.io/badge/View%20Report-➡️-0892ff?style=for-the-badge" alt="view report"/>
  </a>
  &nbsp;
  <a href="#export" style="text-decoration:none">
    <img src="https://img.shields.io/badge/Download%20PDF-⬇️-00aaff?style=for-the-badge" alt="download pdf"/>
  </a>
  &nbsp;
  <a href="#scripts" style="text-decoration:none">
    <img src="https://img.shields.io/badge/Server%20Scripts-⚙️-06b6d4?style=for-the-badge" alt="server scripts"/>
  </a>
</p>
