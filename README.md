// ==============================================================
// 🏗️ FINANZAPLAN COLOMBIA — SISTEMA COMPLETO
// Versión: 1.2 — Constitucional | Autor: Rafael José González Vertel
// Año: 2026 — Fundación, Magdalena, Colombia
// ===============================================================
// 📜 MARCO LEGAL DE REFERENCIA:
// • Constitución Política de Colombia — Artículo 15: Derecho a la intimidad
// • Constitución Política de Colombia — Artículo 20: Derecho a recibir información veraz
// • Ley 1581 de 2012: Protección de Datos Personales
// • Ley 1266 de 2008: Habeas Data Financiero
// • Sentencia T-025 de 2022: Protección de datos en aplicaciones digitales
// • Principio: La información del usuario PERTENECE AL USUARIO, no a esta aplicación
// ===============================================================

// ------------------ DECLARACIÓN DE DERECHOS DEL USUARIO --------
const DECLARACION_DERECHOS_USUARIO = `
╔══════════════════════════════════════════════════════════════╗
║  📜 DECLARACIÓN DE DERECHOS — CONSTITUCIÓN DE COLOMBIA       ║
╠══════════════════════════════════════════════════════════════╣
║  Artículo 15 — Tu intimidad es inviolable                    ║
║  • Nadie puede buscar ni usar tus datos sin tu permiso       ║
║  • Tú decides qué compartir y qué mantener privado          ║
║                                                              ║
║  Artículo 20 — Información clara y transparente             ║
║  • Todo lo que hace esta app está explicado                 ║
║  • No hay letras pequeñas ni condiciones ocultas            ║
║                                                              ║
║  Principio de Autonomía del Dato — Ley 1581/2012            ║
║  • TUS datos son TU propiedad, no de esta aplicación        ║
║  • Puedes acceder, corregir o eliminar tus datos cuando quieras║
║  • En cualquier momento puedes pedir que olvide todo        ║
║                                                              ║
║  PRINCIPIO FUNDAMENTAL:                                      ║
║  "Lo que es tuyo, se queda contigo."                        ║
╚══════════════════════════════════════════════════════════════╝
`;

const COMPROMISO_PRIVACIDAD = `
🔒 COMPROMISO DE PRIVACIDAD ABSOLUTA — FinanzaPlan Colombia

• ✅ No recopilamos información personal identificable
• ✅ No solicitamos cédula, documento de identidad ni datos bancarios
• ✅ No enviamos nada a servidores externos — todo se guarda EN TU DISPOSITIVO
• ✅ No vendemos, intercambiamos ni compartimos información con terceros
• ✅ Tú puedes eliminar TODO en cualquier momento con un solo clic
• ✅ No hay perfiles ocultos ni seguimiento de tu actividad
• ✅ No necesitamos internet para funcionar — funcionas tú solo
`;

// ------------------ CONFIGURACIÓN INICIAL ----------------------
var nombreUsuario = "";
var aceptoTerminos = false;
var mesActual = obtenerMesActual();
var diasUltimoMensajeApoyo = new Date().toISOString();
const AUTOR_APP = "Rafael José González Vertel";
const VERSION_APP = "1.2 — Constitucional";
const VALOR_APORTE_SUGERIDO = 100;
const DATOS_PAGO_APOYO = "Bre-B: 311 691 5445 - 5176920";
const MENSAJE_DESTINO_APORTE = "💛 Tu aporte de $100 COP se divide en dos:\n🔧 50% → Para mejorar esta aplicación de acceso gratuito\n👵 50% → Para ayudar a personas ancianas que lo necesitan\n¡Gracias por hacer el bien con nosotros!";
const SOLO_PARA_COLOMBIA = "🇨🇴 Esta cuenta de apoyo está disponible únicamente para personas dentro de la República de Colombia";
const DIAS_ENTRE_MENSAJES_APOYO = 30;

// ------------------ ALMACENAMIENTO EXCLUSIVO EN DISPOSITIVO ----
// ⚠️ REGLA CONSTITUCIONAL: Los datos NUNCA salen de tu dispositivo
var ingresos = { salario: 0, otros: 0, total: 0 };
var gastos = { vivienda: 0, alimentacion: 0, servicios: 0, transporte: 0, bebe: 0, compras: 0, salud: 0, educacion: 0, otros: 0, total: 0 };
var metasAhorro = [];
var historialMovimientos = [];
var estadoApoyo = { mensajeMostradoEsteMes: false, aporteRealizado: false, cantidadAportesRecibidos: 0 };
var preferenciasUsuario = { tema: "claro", recordarMensajes: true, mostrarConsejos: true };

// ------------------ CÁLCULOS FINANCIEROS ----------------------
function calcularTotalIngresos() { ingresos.total = ingresos.salario + ingresos.otros; return ingresos.total; }
function calcularTotalGastos() { gastos.total = gastos.vivienda + gastos.alimentacion + gastos.servicios + gastos.transporte + gastos.bebe + gastos.compras + gastos.salud + gastos.educacion + gastos.otros; return gastos.total; }
function calcularDisponible() { return calcularTotalIngresos() - calcularTotalGastos(); }
function calcularPorcentajeMeta(montoAcumulado, montoObjetivo) { if (montoObjetivo <= 0) return 0; return Math.min(100, (montoAcumulado / montoObjetivo) * 100); }
function calcularFaltanteMeta(montoAcumulado, montoObjetivo) { return Math.max(0, montoObjetivo - montoAcumulado); }
function calcularAhorroMensualRecomendado(montoFaltante, fechaLimite) { var hoy = new Date(); var metaFecha = new Date(fechaLimite); var mesesRestantes = Math.max(1, ((metaFecha.getFullYear() - hoy.getFullYear()) * 12) + (metaFecha.getMonth() - hoy.getMonth())); return Math.ceil(montoFaltante / mesesRestantes); }
function calcularPromedioGastosUltimos3Meses() { var suma = 0; var contador = 0; var hoy = new Date(); for (var i = 0; i < historialMovimientos.length; i++) { var m = historialMovimientos[i]; if (m.tipo === "GASTO") { var fechaMov = new Date(m.fecha); var mesesDif = (hoy.getFullYear() - fechaMov.getFullYear()) * 12 + (hoy.getMonth() - fechaMov.getMonth()); if (mesesDif <= 3) { suma += m.valor; contador++; } } } return contador > 0 ? Math.round(suma / contador) : 0; }

// ------------------ GESTIÓN DE INGRESOS -----------------------
function registrarIngreso(tipo, monto, descripcion, fecha) { monto = Math.abs(monto); ingresos[tipo] += monto; var fechaReg = fecha || new Date().toLocaleDateString(); historialMovimientos.unshift({ tipo: "INGRESO", categoria: tipo, valor: monto, descripcion: descripcion || "Sin descripción", fecha: fechaReg }); guardarDatosLocalmente(); return "✅ Ingreso registrado correctamente: " + formatearNumero(monto) + " — " + (descripcion || ""); }
function editarIngreso(tipo, nuevoMonto) { ingresos[tipo] = Math.abs(nuevoMonto); guardarDatosLocalmente(); return "✅ Ingreso actualizado correctamente"; }
function eliminarIngreso(tipo, monto, fecha) { ingresos[tipo] -= Math.abs(monto); for (var i = 0; i < historialMovimientos.length; i++) { if (historialMovimientos[i].tipo === "INGRESO" && historialMovimientos[i].categoria === tipo && historialMovimientos[i].valor === monto && historialMovimientos[i].fecha === fecha) { historialMovimientos.splice(i, 1); break; } } guardarDatosLocalmente(); return "✅ Ingreso eliminado del historial — Tú controlas tu información"; }

// ------------------ GESTIÓN DE GASTOS -------------------------
function registrarGasto(categoria, monto, descripcion, fecha) { monto = Math.abs(monto); gastos[categoria] += monto; var fechaReg = fecha || new Date().toLocaleDateString(); historialMovimientos.unshift({ tipo: "GASTO", categoria: categoria, valor: monto, descripcion: descripcion || "Sin descripción", fecha: fechaReg }); guardarDatosLocalmente(); return "✅ Gasto registrado: " + formatearNumero(monto) + " en " + nombreCategoria(categoria); }
function editarGasto(categoria, nuevoMonto) { gastos[categoria] = Math.abs(nuevoMonto); guardarDatosLocalmente(); return "✅ Gasto actualizado correctamente"; }
function eliminarGasto(categoria, monto, fecha) { gastos[categoria] -= Math.abs(monto); for (var i = 0; i < historialMovimientos.length; i++) { if (historialMovimientos[i].tipo === "GASTO" && historialMovimientos[i].categoria === categoria && historialMovimientos[i].valor === monto && historialMovimientos[i].fecha === fecha) { historialMovimientos.splice(i, 1); break; } } guardarDatosLocalmente(); return "✅ Gasto eliminado del historial — Tú decides qué conservar"; }
function nombreCategoria(clave) { var nombres = { vivienda: "Vivienda", alimentacion: "Alimentación", servicios: "Servicios Públicos", transporte: "Transporte", bebe: "Cuidados del Bebé", compras: "Compras y Tiendas", salud: "Salud y Citas Médicas", educacion: "Educación", otros: "Otros Gastos" }; return nombres[clave] || clave; }

// ------------------ GESTIÓN DE METAS DE AHORRO ----------------
function crearMeta(nombre, montoObjetivo, fechaLimite, descripcion) { var nuevaMeta = { id: generarIdUnico(), nombre: nombre, objetivo: Math.abs(montoObjetivo), acumulado: 0, fechaLimite: fechaLimite, descripcion: descripcion || "", fechaCreacion: new Date().toLocaleDateString(), completada: false }; metasAhorro.push(nuevaMeta); guardarDatosLocalmente(); return "🎯 Meta creada: " + nombre + " — Objetivo: " + formatearNumero(montoObjetivo) + " — Meta para: " + fechaLimite; }
function aportarMeta(idMeta, monto) { monto = Math.abs(monto); var meta = buscarMetaPorId(idMeta); if (!meta) return "❌ Meta no encontrada"; meta.acumulado += monto; historialMovimientos.unshift({ tipo: "META", nombreMeta: meta.nombre, valor: monto, fecha: new Date().toLocaleDateString() }); if (meta.acumulado >= meta.objetivo && !meta.completada) { meta.completada = true; return "🎉 ¡META COMPLETADA! " + meta.nombre + " alcanzó su objetivo. Felicidades!"; } guardarDatosLocalmente(); var porcentaje = calcularPorcentajeMeta(meta.acumulado, meta.objetivo); var faltante = calcularFaltanteMeta(meta.acumulado, meta.objetivo); var mensual = calcularAhorroMensualRecomendado(faltante, meta.fechaLimite); return "✅ Aporte realizado. " + meta.nombre + ": " + porcentaje.toFixed(1) + "% completado. Faltan: " + formatearNumero(faltante) + " — Ahorra aprox. " + formatearNumero(mensual) + "/mes para lograrlo a tiempo."; }
function buscarMetaPorId(id) { for (var i = 0; i < metasAhorro.length; i++) { if (metasAhorro[i].id === id) return metasAhorro[i]; } return null; }
function eliminarMeta(idMeta) { for (var i = 0; i < metasAhorro.length; i++) { if (metasAhorro[i].id === idMeta) { metasAhorro.splice(i, 1); guardarDatosLocalmente(); return "✅ Meta eliminada — Tú decides tus metas"; } } return "❌ Meta no encontrada"; }
function generarIdUnico() { return Math.random().toString(36).substring(2, 10).toUpperCase(); }

// ------------------ SISTEMA DE APOYO — VOLUNTARIO ------------
function mensajeApoyoProyecto() { return "\n╔══════════════════════════════════════════════════════════╗\n║              💛 FINANZAPLAN COLOMBIA — APOYO              ║\n║                Creada por: " + AUTOR_APP.padEnd(36, " ") + "║\n║                    Versión " + VERSION_APP + "                      ║\n╠══════════════════════════════════════════════════════════╣\n║  Esta aplicación es y será SIEMPRE GRATUITA. No hay      ║\n║  funciones bloqueadas, no hay versiones pagas obligatorias.║\n║                                                           ║\n║  Si te sirve esta herramienta y puedes, un aporte        ║\n║  voluntario de $100 COP nos ayuda de dos formas:          ║\n║                                                           ║\n║   🔧 50% → Mejorar y mantener la aplicación gratuita     ║\n║   👵 50% → Ayudar a personas ancianas en nuestra comunidad║\n║                                                           ║\n║  💳 Datos de aporte: " + DATOS_PAGO_APOYO.padEnd(38, " ") + "║\n║  💵 Valor sugerido: $100 COP                              ║\n║  🇨🇴 " + SOLO_PARA_COLOMBIA.padEnd(54, " ") + "║\n║                                                           ║\n║  ✨ Recuerda: No es obligatorio. No pierdes nada si no   ║\n║  aportas. Tu acceso es completo, libre y tuyo siempre.    ║\n╚══════════════════════════════════════════════════════════╝\n  [1] Ya realicé mi aporte ✅    [2] Recordarme después\n  [3] No por ahora"; }
function mostrarMensajeApoyo() { if (!debeMostrarMensajeApoyo()) return false; var respuesta = mostrarDialogo(mensajeApoyoProyecto()); if (respuesta === "1") { estadoApoyo.aporteRealizado = true; estadoApoyo.mensajeMostradoEsteMes = true; estadoApoyo.cantidadAportesRecibidos++; guardarDatosLocalmente(); return "\n" + MENSAJE_DESTINO_APORTE + "\n\n💛 ¡Gracias de corazón por hacer la diferencia!"; } else if (respuesta === "2") { diasUltimoMensajeApoyo = new Date().toISOString(); guardarDatosLocalmente(); return "✅ Perfecto, te recordaré más adelante. Sin presión, siempre a tu ritmo."; } else if (respuesta === "3") { estadoApoyo.mensajeMostradoEsteMes = true; guardarDatosLocalmente(); return "✅ Entendido. Sin presión. La app sigue siendo tuya, completa y libre."; } return true; }
function debeMostrarMensajeApoyo() { if (estadoApoyo.aporteRealizado) return false; if (estadoApoyo.mensajeMostradoEsteMes) return false; var hoy = new Date(); var ultimo = new Date(diasUltimoMensajeApoyo); var diasTranscurridos = Math.floor((hoy - ultimo) / (1000 * 60 * 60 * 24)); return diasTranscurridos >= DIAS_ENTRE_MENSAJES_APOYO; }

// ------------------ ARTÍCULO 15 — PROTECCIÓN DE TUS DATOS -----
// ⚖️ Implementación del Artículo 15 de la Constitución Política
function guardarDatosLocalmente() { var datos = { nombreUsuario: nombreUsuario, versionApp: VERSION_APP, fechaUltimaModificacion: new Date().toLocaleDateString(), ingresos: ingresos, gastos: gastos, metasAhorro: metasAhorro, historial: historialMovimientos, preferencias: preferenciasUsuario, estadoApoyo: estadoApoyo, fechaUltimoApoyo: diasUltimoMensajeApoyo }; localStorage.setItem("FinanzaPlan_Colombia", JSON.stringify(datos)); }
function cargarDatosLocalmente() { var guardado = localStorage.getItem("FinanzaPlan_Colombia"); if (guardado) { try { var datos = JSON.parse(guardado); nombreUsuario = datos.nombreUsuario || ""; ingresos = datos.ingresos || { salario: 0, otros: 0, total: 0 }; gastos = datos.gastos || { vivienda: 0, alimentacion: 0, servicios: 0, transporte: 0, bebe: 0, compras: 0, salud: 0, educacion: 0, otros: 0 }; metasAhorro = datos.metasAhorro || []; historialMovimientos = datos.historial || []; preferenciasUsuario = datos.preferencias || { tema: "claro", recordarMensajes: true, mostrarConsejos: true }; estadoApoyo = datos.estadoApoyo || { mensajeMostradoEsteMes: false, aporteRealizado: false, cantidadAportesRecibidos: 0 }; diasUltimoMensajeApoyo = datos.fechaUltimoApoyo || new Date().toISOString(); return true; } catch (e) { return false; } } return false; }
function solicitarAutorizacionTratamientoDatos() { var explicacion = DECLARACION_DERECHOS_USUARIO + "\n" + COMPROMISO_DE_PRIVACIDAD + "\n\nAl usar esta aplicación, comprendes que:\n• Toda la información que registres se almacena ÚNICAMENTE en este dispositivo\n• Ningún dato viaja a servidores externos ni se comparte con terceros\n• Puedes eliminar toda tu información en cualquier momento\n• Esta herramienta no reemplaza asesoría financiera oficial\n\n¿Entiendes y aceptas que tus datos son responsabilidad tuya y se guardan solo aquí?\n[1] Sí, entiendo y acepto  [2] No, deseo salir de la aplicación"; var respuesta = mostrarDialogo(explicacion); if (respuesta === "1") { aceptoTerminos = true; guardarDatosLocalmente(); return true; } else { return false; } }
function ejercicioDerechoHabeasData(opcion) { switch (opcion) { case "CONSULTAR": var misDatos = { ingresos: ingresos, gastos: gastos, metas: metasAhorro, historial: historialMovimientos }; return JSON.stringify(misDatos, null, 2); case "CORREGIR": return "✏️ Para corregir información, simplemente edita el dato que consideras incorrecto. Tú tienes el control directo."; case "ELIMINAR_TODO": if (confirmar("⚠️ EJERCICIO DE TU DERECHO — Artículo 15 Constitución de Colombia\n¿Deseas que esta aplicación elimine TODA tu información de forma permanente? Esta acción no se puede deshacer.")) { localStorage.removeItem("FinanzaPlan_Colombia"); ingresos = { salario: 0, otros: 0, total: 0 }; gastos = { vivienda: 0, alimentacion: 0, servicios: 0, transporte: 0, bebe: 0, compras: 0, salud: 0, educacion: 0, otros: 0 }; metasAhorro = []; historialMovimientos = []; estadoApoyo = { mensajeMostradoEsteMes: false, aporteRealizado: false, cantidadAportesRecibidos: 0 }; return "✅ 📜 Artículo 15 — Tu derecho ha sido ejercido. Tu información ha sido eliminada.\nLa aplicación no guarda copias. Lo que fue tuyo, ahora descansa en el olvido. Gracias por usarla."; } return "Operación cancelada. Tu información sigue bajo tu control."; default: return "📜 Artículo 15 — Tú tienes el derecho de decidir sobre tu información."; } }
function exportarMisDatos() { var misDatos = { autorApp: AUTOR_APP, version: VERSION_APP, fechaExportacion: new Date().toLocaleDateString(), declaracionDerechos: "Artículo 15 Constitución Política de Colombia", resumen: { ingresos: ingresos, gastos: gastos, disponible: calcularDisponible() }, metas: metasAhorro, historial: historialMovimientos, transparencia: { autor: AUTOR_APP, destinoAportes: "50% Mejoras — 50% Adultos Mayores", cuentaApoyo: DATOS_PAGO_APOYO, ambito: SOLO_PARA_COLOMBIA } }; var texto = JSON.stringify(misDatos, null, 2); var elemento = document.createElement("a"); elemento.setAttribute("href", "data:text/plain;charset=utf-8," + encodeURIComponent(texto)); elemento.setAttribute("download", "MIS_DATOS_FinanzaPlan_" + mesActual.replace(" ", "_") + ".txt"); document.body.appendChild(elemento); elemento.click(); document.body.removeChild(elemento); return "✅ 📜 Descarga completada. Esta información es propiedad tuya, respaldada por el Artículo 15 de la Constitución de Colombia."; }

// ------------------ REPORTES Y RESÚMENES ---------------------
function formatearBarraProgreso(porcentaje, longitud) { longitud = longitud || 20; var lleno = Math.round((porcentaje / 100) * longitud); var vacio = longitud - lleno; var barra = ""; for (var i = 0; i < lleno; i++) barra += "█"; for (var j = 0; j < vacio; j++) barra += "░"; return barra + " " + porcentaje.toFixed(1) + "%"; }
function generarReporteCompleto() { var hoy = new Date().toLocaleDateString(); var reporte = "\n╔══════════════════════════════════════════════════════════════╗\n║           🇨🇴 FINANZAPLAN COLOMBIA — REPORTE COMPLETO          ║\n║          Creado por: " + AUTOR_APP.substring(0, 42).padEnd(42, " ") + "║\n║             Versión " + VERSION_APP + " — " + hoy.padEnd(28, " ") + "║\n╠══════════════════════════════════════════════════════════════╣\n║  📜 Artículo 15 Constitución de Colombia: Esta información   ║\n║  es propiedad tuya. Tú eres dueño de ella.                     ║\n╠══════════════════════════════════════════════════════════════╣\n║                        📊 RESUMEN DEL MES                      ║\n╠══════════════════════════════════════════════════════════════╣\n║  💰 INGRESOS TOTALES:  " + formatearNumero(calcularTotalIngresos()).padEnd(40, " ") + "║\n║  💸 GASTOS TOTALES:    " + formatearNumero(calcularTotalGastos()).padEnd(40, " ") + "║\n║  💵 DISPONIBLE:        " + formatearNumero(calcularDisponible()).padEnd(40, " ") + "║\n║  📈 PROMEDIO GASTOS 3M: " + formatearNumero(calcularPromedioGastosUltimos3Meses()).padEnd(40, " ") + "║\n║  🎯 METAS ACTIVAS:     " + String(metasAhorro.length).padEnd(40, " ") + "║\n╠══════════════════════════════════════════════════════════════╣\n║                      📋 DESGLOSE POR CATEGORÍA                 ║\n╠══════════════════════════════════════════════════════════════╣\n"; for (var clave in gastos) { if (gastos[clave] > 0) { reporte += "║  " + nombreCategoria(clave).padEnd(18, " ") + ": " + formatearNumero(gastos[clave]).padEnd(42, " ") + "║\n"; } } reporte += "╠══════════════════════════════════════════════════════════════╣\n║                       🎯 METAS DE AHORRO                       ║\n╠══════════════════════════════════════════════════════════════╣\n"; if (metasAhorro.length === 0) { reporte += "║  No tienes metas de ahorro activas todavía.                  ║\n║  ¡Crea una y empieza a planear tus sueños! 💫                 ║\n"; } else { for (var i = 0; i < metasAhorro.length; i++) { var m = metasAhorro[i]; var p = calcularPorcentajeMeta(m.acumulado, m.objetivo); var f = calcularFaltanteMeta(m.acumulado, m.objetivo)
