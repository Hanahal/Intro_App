#  Cemented Rock Fill (CRF) – Sistema de Análisis Geomecánico y Diseño de Mezclas

##  Descripción General

Este proyecto es una aplicación desarrollada en **Streamlit** orientada al análisis, diseño y evaluación de sistemas de **Cemented Rock Fill (CRF)** aplicados a minería subterránea bajo el método **Sub Level Stoping**.

La herramienta integra cálculos de:
- Diseño de mezclas de relleno cementado
- Resistencia a compresión (UCS)
- Infraestructura de cámaras de mezclado
- Modelos geomecánicos (Mitchell y Dujisin)
- Generación de reportes técnicos en PDF
- Visualización avanzada con Plotly y Matplotlib

---

##  Objetivo del Sistema

El objetivo principal es proporcionar una plataforma técnica para:

- Evaluar la viabilidad de diseños CRF
- Comparar mezclas cementadas
- Analizar comportamiento mecánico del relleno
- Dimensionar infraestructura minera asociada
- Generar reportes automáticos de ingeniería

---

 Alcance del sistema (Parte 1)

Este módulo cubre:

✔ Diseño de mezclas CRF
✔ Propiedades físicas de materiales
✔ Infraestructura minera básica
✔ Modelado geométrico del tajo
✔ Preparación de datos técnicos
✔ Exportación de datasets estructurados
 Nota técnica

Este sistema está diseñado para aplicaciones de:

Ingeniería de minas subterráneas
Diseño de relleno cementado (CRF)
Estudios de estabilidad geomecánica
Simulación de infraestructura minera
Generación de reportes técnicos profesionales
 Autor

Bradoc Chambilla


---

Cuando quieras, envíame la **Parte 2** y te completo el README con:

- 📊 :contentReference[oaicite:0]{index=0}
- 📄 :contentReference[oaicite:1]{index=1}
- 🧮 :contentReference[oaicite:2]{index=2}
- 📈 :contentReference[oaicite:3]{index=3}
- 🧾 :contentReference[oaicite:4]{index=4}
- 🚀 :contentReference[oaicite:5]{index=5}

Solo envíala 👍
y la parte  2  final: # ═══════════════════════════════════════════════════════════
#  SESSION STATE
# ═══════════════════════════════════════════════════════════
if "csv_activo"  not in st.session_state: st.session_state.csv_activo  = False
if "pendiente"   not in st.session_state: st.session_state.pendiente   = -1
if "designs_df"  not in st.session_state:
    st.session_state.designs_df = pd.DataFrame({
        "Diseño":["D01","D02","D03","D04"],
        "Cemento (kg/m³)":[75.,90.,103.,138.], "Agua (L/m³)":[60.,72.,82.,110.],
        "Desmonte (kg/m³)":[2812.,2760.,2717.,2595.], "Densidad (kg/m³)":[2947.,2922.,2902.,2843.],
    })
if "resist_df" not in st.session_state:
    st.session_state.resist_df = pd.DataFrame({
        "Días":[7,14,21,28,56],
        "D01":[0.65,1.01,1.39,1.66,1.68],"D02":[1.07,1.37,1.63,2.08,2.11],
        "D03":[1.68,2.73,3.38,4.82,4.95],"D04":[2.58,3.14,4.41,5.21,6.53],
    })
if "espec" not in st.session_state: st.session_state.espec = DEFAULT_ESPEC.copy()
if "agre"  not in st.session_state: st.session_state.agre  = DEFAULT_AGRE.copy()
if "infra" not in st.session_state: st.session_state.infra = DEFAULT_INFRA.copy()
if "geo"   not in st.session_state: st.session_state.geo   = DEFAULT_GEO.copy()
 
 
# ═══════════════════════════════════════════════════════════
#  CARGA CSV
# ═══════════════════════════════════════════════════════════
st.markdown("### 📂 Carga de Datos Maestros")
col_info, col_dl = st.columns([3,1])
with col_info:
    st.info("**Paso 1:** Descargue la plantilla CSV → **Paso 2:** Complete sus datos → **Paso 3:** Suba el archivo aquí.")
with col_dl:
    st.download_button("⬇️ Descargar Plantilla CSV", data=generate_template_csv(),
        file_name="plantilla_CRF_maestro.csv", mime="text/csv", use_container_width=True)
 
uploaded_csv = st.file_uploader("Seleccionar archivo CSV maestro (.csv)", type=["csv"], key="master_csv")
if uploaded_csv is not None:
    try:
        sections = parse_master_csv(uploaded_csv)
        def load_kv(sec, default):
            if sec not in sections: return default.copy()
            df_ = sections[sec].copy(); df_.columns = ["Parametro","Valor"]
            d = default.copy()
            for _, r in df_.iterrows():
                k = str(r["Parametro"]).strip()
                if k in d: d[k] = float(r["Valor"])
            return d
        if "DESIGNS" in sections:
            dc = sections["DESIGNS"].copy()
            dc.columns = ["Diseño","Cemento (kg/m³)","Agua (L/m³)","Desmonte (kg/m³)","Densidad (kg/m³)"]
            for col in dc.columns[1:]: dc[col] = pd.to_numeric(dc[col], errors="coerce")
            st.session_state.designs_df = dc.reset_index(drop=True)
        if "RESISTENCIAS" in sections:
            dr = sections["RESISTENCIAS"].copy(); dr.columns = ["Días","D01","D02","D03","D04"]
            for col in dr.columns: dr[col] = pd.to_numeric(dr[col], errors="coerce")
            st.session_state.resist_df = dr.reset_index(drop=True)
        st.session_state.espec = load_kv("ESPECIFICACIONES", DEFAULT_ESPEC)
        st.session_state.agre  = load_kv("AGREGADO",        DEFAULT_AGRE)
        st.session_state.infra = load_kv("INFRAESTRUCTURA", DEFAULT_INFRA)
        st.session_state.geo   = load_kv("GEOMECANICA",     DEFAULT_GEO)
        st.session_state.csv_activo = True
        st.success(f"✔ CSV cargado — secciones: {', '.join(sections.keys())}.")
    except Exception as e:
        st.error(f"❌ Error al procesar el CSV: {e}")
 
if st.session_state.csv_activo:
    if st.button("🔄 Restaurar datos por defecto"):
        for k in ["csv_activo","designs_df","resist_df","espec","agre","infra","geo"]:
            if k in st.session_state: del st.session_state[k]
        st.rerun()
 
st.markdown("---")
 
 
# ═══════════════════════════════════════════════════════════
#  TABS
# ═══════════════════════════════════════════════════════════
tabs = st.tabs(["🧪 Diseños de Mezcla","📐 Diseño Seleccionado",
                "🏗️ Infraestructura","📊 Cálculos Geomecánicos"])
 
 
# ╔══════════════════════════════════════════════╗
#  TAB 1 — DISEÑOS DE MEZCLA
# ╚══════════════════════════════════════════════╝
with tabs[0]:
    st.markdown("###  Diseños de Mezcla — Prueba Piloto")
    df        = st.session_state.designs_df.copy()
    resist_df = st.session_state.resist_df.copy()
    df["Total"]       = df["Cemento (kg/m³)"]+df["Agua (L/m³)"]+df["Desmonte (kg/m³)"]
    df["Cemento (%)"] = (df["Cemento (kg/m³)"]/df["Total"]*100).round(2)
    df["Agua (%)"]    = (df["Agua (L/m³)"]/df["Total"]*100).round(2)
    df["Desmonte (%)"]= (df["Desmonte (kg/m³)"]/df["Total"]*100).round(2)
    df["A/C"]         = (df["Agua (L/m³)"]/df["Cemento (kg/m³)"]).round(3)
 
    st.markdown("#### Diseño de Mezcla (kg/m³ y L/m³)")
    st.markdown(render_table(df[["Diseño","Cemento (kg/m³)","Cemento (%)","Agua (L/m³)","Agua (%)",
                                  "Desmonte (kg/m³)","Desmonte (%)","Densidad (kg/m³)","A/C"]]),
                unsafe_allow_html=True)
    st.markdown("<br>", unsafe_allow_html=True)
    st.markdown("#### Desarrollo de Resistencia a Compresión (MPa)")
    st.markdown(render_table(resist_df), unsafe_allow_html=True)
    st.session_state.designs_df = df
 
    st.markdown("### 🎯 Seleccionar Diseño")
    selected = st.selectbox("Seleccione un diseño:", df["Diseño"].tolist(), key="select_design")
    st.session_state.selected_design = selected
    st.info(f"📌 Diseño seleccionado: **{selected}**")
 
    # ── GRÁFICOS EN PARALELO ─────────────────────────────────────────
    st.markdown("###  Curva de Resistencia · Resistencia a 28 días")
    gc1, gc2 = st.columns(2)
 
    with gc1:
        fig_res = go.Figure()
        for d, color in zip(["D01","D02","D03","D04"], COLORS):
            fig_res.add_trace(go.Scatter(
                x=resist_df["Días"], y=resist_df[d],
                mode="lines+markers", name=d,
                opacity=1.0 if d==selected else 0.3,
                line=dict(color=color, width=4 if d==selected else 2)
            ))
        fig_res.update_layout(**PLOTLY_LAYOUT, height=400,
            title=dict(text=f"Curva de Resistencia — {selected}", font=dict(size=15)),
            xaxis_title="Días", yaxis_title="MPa")
        st.plotly_chart(fig_res, use_container_width=True)
 
    with gc2:
        res_28 = resist_df[resist_df["Días"]==28].iloc[0]
        fig_28 = go.Figure()
        for d, color in zip(["D01","D02","D03","D04"], COLORS):
            fig_28.add_trace(go.Bar(
                x=[d], y=[res_28[d]], marker_color=color,
                opacity=1.0 if d==selected else 0.25,
                text=[f"{res_28[d]:.2f} MPa"], textposition="outside",
                textfont=dict(size=14)
            ))
        fig_28.update_layout(**PLOTLY_LAYOUT,
            title=dict(text=f"Resistencia 28d — {selected}", font=dict(size=15)),
            height=400, showlegend=False, yaxis_title="MPa")
        st.plotly_chart(fig_28, use_container_width=True)
 
 
# ╔══════════════════════════════════════════════╗
#  TAB 2 — DISEÑO SELECCIONADO
# ╚══════════════════════════════════════════════╝
with tabs[1]:
    st.markdown("##  Diseño de Mezcla Seleccionado")
    if "selected_design" not in st.session_state:
        st.warning("Seleccione un diseño en la pestaña anterior."); st.stop()
 
    selected = st.session_state.selected_design
    st.markdown(f"### 🎯 DISEÑO: **{selected}**")
    df_all = st.session_state.designs_df.copy()
    if "A/C" not in df_all.columns:
        df_all["A/C"] = (df_all["Agua (L/m³)"]/df_all["Cemento (kg/m³)"]).round(3)
    df_resist = st.session_state.resist_df.copy()
    espec=st.session_state.espec; agre=st.session_state.agre
    row=df_all[df_all["Diseño"]==selected].iloc[0]
    cem=float(row["Cemento (kg/m³)"]); desm=float(row["Desmonte (kg/m³)"])
    rel_ac=float(row["A/C"]); resist_28=float(df_resist[df_resist["Días"]==28][selected].values[0])
    agua_ind=float(espec["Agua_industrial_Lm3"]); aire_at=int(espec["Aire_atrapado_pct"])
    hum=float(agre["Humedad_natural_pct"]); absorc=float(agre["Absorcion_pct"])
    peso_esp_cem=float(agre["Peso_especifico_cemento_kgm3"]); dens_agua=float(agre["Densidad_agua_kgm3"])
    agg_corr=desm/(1+hum/100); aporte=agg_corr*((hum-absorc)/100)
    dens_m=cem+agua_ind+aporte+agg_corr
 
    st.markdown("###  Especificaciones y Características del Agregado")
    c1, c2 = st.columns(2)
    with c1:
        st.markdown("**Especificaciones del Diseño**")
        st.markdown(render_table(pd.DataFrame({
            "Parámetro":["F'c (kg/cm²)","F'cr (kg/cm²)","Slump (pulg)","Agua ind. (L/m³)","Aire (%)","A/C"],
            "Valor":[f"{espec['Fc_kgcm2']:.0f}",f"{espec['Fcr_kgcm2']:.0f}",f"{espec['Slump_pulg']:.1f}",
                     f"{agua_ind:.1f}",f"{aire_at}",f"{rel_ac:.3f}"]
        })), unsafe_allow_html=True)
    with c2:
        st.markdown("**Características del Agregado**")
        st.markdown(render_table(pd.DataFrame({
            "Parámetro":["Tam. máx. (pulg)","Tam. nom. (pulg)","Mód. fineza",
                         "P.U. suelto (kg/m³)","P.U. compact. (kg/m³)","P.E. agr. (kg/m³)","Hum. nat. (%)","Absorción (%)"],
            "Valor":[f"{agre['Tamano_maximo_pulg']:.2f}",f"{agre['Tamano_nominal_pulg']:.2f}",f"{agre['Modulo_fineza']:.2f}",
                     f"{agre['Peso_unitario_suelto_kgm3']:.0f}",f"{agre['Peso_unitario_compactado_kgm3']:.0f}",
                     f"{agre['Peso_especifico_agregado_kgm3']:.0f}",f"{agre['Humedad_natural_pct']:.2f}",f"{agre['Absorcion_pct']:.2f}"]
        })), unsafe_allow_html=True)
 
    st.markdown("###  Adicionados y Cálculo de Mezcla")
    c3, c4 = st.columns(2)
    with c3:
        st.markdown("**Adicionados**")
        st.markdown(render_table(pd.DataFrame({
            "Propiedad":["P.E. cemento (kg/m³)","Densidad agua (kg/m³)"],
            "Valor":[f"{peso_esp_cem:.0f}",f"{dens_agua:.2f}"]
        })), unsafe_allow_html=True)
    with c4:
        st.markdown("**Cálculo del Diseño de Mezcla**")
        st.markdown(render_table(pd.DataFrame({
            "Concepto":["Cemento (kg/m³)","Agua ind. (L/m³)","Agg. corregido (kg/m³)",
                        "Aporte agua (L/m³)","Aire atrapado (%)","Densidad mezcla (kg/m³)"],
            "Valor":[f"{cem:.2f}",f"{agua_ind:.2f}",f"{agg_corr:.2f}",
                     f"{aporte:.2f}",f"{aire_at}%",f"{dens_m:.2f}"]
        })), unsafe_allow_html=True)
 
    st.markdown("### 🧮 Cálculo por Tanda")
    tanda = st.number_input("Tanda (m³ por ciclo)", value=10.0, step=0.5)
    st.markdown(render_table(pd.DataFrame({
        "Material":["Cemento","Agua (total)","Agregado"],
        "Por tanda":[f"{cem*tanda:.2f}",f"{(agua_ind+aporte)*tanda:.2f}",f"{agg_corr*tanda:.2f}"]
    })), unsafe_allow_html=True)
 
    st.markdown("###  Dosificación de Mezcla — Resumen Final")
    st.markdown(render_table(pd.DataFrame({
        "Componente":["Cemento","Desmonte","Agua industrial","Relación A/C","Densidad mezcla final"],
        "Valor":[f"{cem:.2f} kg/m³",f"{desm:.2f} kg/m³",f"{agua_ind:.2f} L/m³",f"{rel_ac:.3f}",f"{dens_m:.2f} kg/m³"]
    })), unsafe_allow_html=True)
 
    st.markdown("###  Gráficos del Diseño Seleccionado")
    cg1, cg2 = st.columns(2)
    with cg1:
        fig_pie = go.Figure(go.Pie(labels=["Cemento","Agua","Agregado"],
            values=[cem,agua_ind+aporte,agg_corr], hole=0.35,
            textfont=dict(size=14)))
        fig_pie.update_layout(**PLOTLY_LAYOUT, title=dict(text=f"Composición — {selected}", font=dict(size=15)))
        st.plotly_chart(fig_pie, use_container_width=True)
    with cg2:
        fig_bar = go.Figure(go.Bar(x=["Cemento","Agua total","Agregado"],
            y=[cem,agua_ind+aporte,agg_corr], marker_color=["#3fb950","#388bfd","#f0a500"],
            text=[f"{cem:.1f}",f"{agua_ind+aporte:.1f}",f"{agg_corr:.1f}"],
            textposition="outside", textfont=dict(size=14)))
        fig_bar.update_layout(**PLOTLY_LAYOUT,
            title=dict(text="Componentes por m³", font=dict(size=15)), yaxis_title="kg o L")
        st.plotly_chart(fig_bar, use_container_width=True)
 
    if st.button("💾 Guardar Diseño"):
        st.session_state.saved_mix = {"diseño":selected,"cemento":cem,"agua":agua_ind,
            "agregado":desm,"rel_ac":rel_ac,"densidad_mezcla":dens_m,"aire":aire_at,"resistencia_28d":resist_28}
        st.success("✔ Diseño guardado correctamente.")
 
 
# ╔══════════════════════════════════════════════╗
#  TAB 3 — INFRAESTRUCTURA
# ╚══════════════════════════════════════════════╝
with tabs[2]:
    st.markdown("##  Infraestructura · Cámara de Mezclado del CRF")
    if st.session_state.csv_activo:
        st.success("📂 Parámetros cargados desde el CSV maestro.")
    else:
        st.info("📋 Mostrando datos por defecto.")
 
    infra = st.session_state.infra
    largo=float(infra["Largo_camara_m"]); ancho=float(infra["Ancho_util_m"]); alto=float(infra["Alto_util_m"])
    esp_conc=float(infra["Espesor_concreto_m"]); esp_shot=float(infra["Espesor_shotcrete_m"])
    esp_rell=float(infra["Espesor_relleno_estructural_m"])
    alt_buz=float(infra["Altura_buzon_m"]); anc_ban=float(infra["Ancho_bandeja_m"])
 
    pendiente = st.selectbox("Pendiente del piso (%)", [-1, -2])
    st.session_state.pendiente = pendiente
 
    # Fila 1: Equipos | Personal
    st.markdown("###  Equipos y Personal")
    c1, c2 = st.columns(2)
    with c1:
        st.markdown("**Equipos Principales**")
        st.markdown(render_table(pd.DataFrame({
            "Equipo":["Bomba estacionaria","Chancadora secundaria","Faja transportadora","Tolva de regulación"],
            "Capacidad":[f"{infra['Bomba_estacionaria_m3h']:.0f} m³/h",f"{infra['Chancadora_secundaria_th']:.0f} t/h",
                         f"{infra['Faja_transportadora_th']:.0f} t/h",f"{infra['Tolva_regulacion_m3']:.0f} m³"]
        })), unsafe_allow_html=True)
    with c2:
        st.markdown("**Personal Directo Requerido**")
        st.markdown(render_table(pd.DataFrame({
            "Cargo":["Operador","Mecánico","Supervisor"],
            "Cantidad":[int(infra["Personal_operadores"]),int(infra["Personal_mecanicos"]),int(infra["Personal_supervisores"])]
        })), unsafe_allow_html=True)
 
    # Fila 2: Geometría | Volúmenes
    piso_drop=largo*(abs(pendiente)/100)
    vol_util=largo*ancho*alto; vol_conc=largo*ancho*esp_conc
    vol_exc=(ancho+2*esp_conc)*(alto+2*esp_conc)*largo
 
    st.markdown("###  Geometría y Volúmenes")
    c3, c4 = st.columns(2)
    with c3:
        st.markdown("**Geometría de la Cámara**")
        st.markdown(render_table(pd.DataFrame({
            "Parámetro":["Longitud (m)","Ancho útil (m)","Altura útil (m)","Pendiente piso (%)",
                         "Esp. concreto (m)","Esp. shotcrete (m)","Esp. relleno (m)","Altura buzón (m)","Ancho bandeja (m)"],
            "Valor":[largo,ancho,alto,pendiente,esp_conc,esp_shot,esp_rell,alt_buz,anc_ban]
        })), unsafe_allow_html=True)
    with c4:
        st.markdown("**Cálculos de Volúmenes**")
        st.markdown(render_table(pd.DataFrame({
            "Concepto":["Volumen útil (m³)","Caída del piso (m)","Vol. concreto (m³)","Vol. excavado (m³)"],
            "Valor":[f"{vol_util:.2f}",f"{piso_drop:.2f}",f"{vol_conc:.2f}",f"{vol_exc:.2f}"]
        })), unsafe_allow_html=True)
 
    # ── GRÁFICO TÉCNICO ──────────────────────────────────────────────
    st.markdown("##  Perfil Técnico — Cámara de Mezclado CRF")
    fig_tec = draw_mining_profile(largo, ancho, alto, esp_conc, esp_shot,
                                  esp_rell, alt_buz, anc_ban, pendiente)
    st.pyplot(fig_tec, use_container_width=True)
    plt.close(fig_tec)
 
 
# ╔══════════════════════════════════════════════╗
#  TAB 4 — CÁLCULOS GEOMECÁNICOS
# ╚══════════════════════════════════════════════╝
with tabs[3]:
    st.markdown("##  Cálculos Geomecánicos — Método Mitchell + Dujisin")
    if st.session_state.csv_activo:
        st.success("📂 Parámetros cargados desde el CSV maestro.")
    else:
        st.info("📋 Mostrando datos por defecto.")
 
    geo=st.session_state.geo
    H=float(geo["Altura_caseron_H_m"]); B=float(geo["Ancho_caseron_B_m"]); L=float(geo["Longitud_caseron_L_m"])
    dens_rell=float(geo["Densidad_CRF_kgm3"]); cohes=float(geo["Cohesion_CRF_kPa"])
    phi=float(geo["Angulo_friccion_phi_deg"]); dens_roca=float(geo["Densidad_roca_kgm3"])
    fs=float(geo["Factor_seguridad"]); poisson=float(geo["Coeficiente_Poisson"])
    gamma_rell=dens_rell*9.81/1e6; gamma_roca=dens_roca*9.81/1e6
    K=(1-math.sin(math.radians(phi)))/(1+math.sin(math.radians(phi)))
    sigma_v=gamma_rell*H; sigma_h=K*sigma_v
    sigma_techo=sigma_v/fs; sigma_lat=sigma_h/fs; sigma_tot=sigma_techo+sigma_lat
 
    st.markdown("###  Parámetros de Entrada")
    c1, c2 = st.columns(2)
    with c1:
        st.markdown("**Geometría y Materiales**")
        st.markdown(render_table(pd.DataFrame({
            "Parámetro":["Altura caserón H (m)","Ancho caserón B (m)","Longitud caserón L (m)",
                         "Densidad CRF (kg/m³)","Cohesión CRF (kPa)"],
            "Valor":[f"{H:.1f}",f"{B:.1f}",f"{L:.1f}",f"{dens_rell:.0f}",f"{cohes:.0f}"]
        })), unsafe_allow_html=True)
    with c2:
        st.markdown("**Propiedades Geomecánicas**")
        st.markdown(render_table(pd.DataFrame({
            "Parámetro":["Ángulo fricción φ (°)","Densidad roca (kg/m³)","Factor de Seguridad","Coef. Poisson ν"],
            "Valor":[f"{phi:.1f}",f"{dens_roca:.0f}",f"{fs}",f"{poisson:.2f}"]
        })), unsafe_allow_html=True)
 
    st.markdown("###  Conversión y Mitchell (1970)")
    c3, c4 = st.columns(2)
    with c3:
        st.markdown("**Pesos Unitarios**")
        st.markdown(render_table(pd.DataFrame({
            "Material":["Relleno CRF","Roca encajonante"],
            "Densidad (kg/m³)":[dens_rell,dens_roca],
            "γ (MN/m³)":[f"{gamma_rell:.6f}",f"{gamma_roca:.6f}"]
        })), unsafe_allow_html=True)
    with c4:
        st.markdown("**Cálculos Mitchell (1970)**")
        st.markdown(render_table(pd.DataFrame({
            "Concepto":["Sobrecarga vertical σv (MN/m²)","Coef. activo K","Presión lateral σh (MN/m²)"],
            "Valor":[f"{sigma_v:.4f}",f"{K:.4f}",f"{sigma_h:.4f}"]
        })), unsafe_allow_html=True)
 
    st.markdown("###  Dujisin (1992) y Resultado")
    c5, c6 = st.columns(2)
    with c5:
        st.markdown("**Presiones Reducidas**")
        st.markdown(render_table(pd.DataFrame({
            "Concepto":["σ techo reducida (MN/m²)","σ lateral reducida (MN/m²)","Factor de Seguridad"],
            "Valor":[f"{sigma_techo:.4f}",f"{sigma_lat:.4f}",f"{fs}"]
        })), unsafe_allow_html=True)
    with c6:
        st.markdown("**Presión Total Actuante**")
        st.markdown(render_table(pd.DataFrame({
            "Concepto":["σ total sobre caserón (MN/m²)"],
            "Valor":[f"{sigma_tot:.4f}"]
        })), unsafe_allow_html=True)
 
    st.info(f"📌 **Presión total actuante sobre el caserón:** {sigma_tot:.4f} MN/m²")
 
    st.markdown("###  Gráficos Geomecánicos")
    cg1, cg2 = st.columns(2)
    z = np.linspace(0, H, 50)
    with cg1:
        fig1 = go.Figure()
        fig1.add_trace(go.Scatter(x=K*gamma_rell*z, y=z, name="Mitchell",
            mode="lines", line=dict(color="#f0a500",width=3)))
        fig1.add_trace(go.Scatter(x=K*gamma_rell*z/fs, y=z, name="Dujisin",
            mode="lines", line=dict(color="#3fb950",width=3,dash="dash")))
        fig1.update_layout(**PLOTLY_LAYOUT,
            title=dict(text="Presión Lateral (MN/m²)", font=dict(size=15)),
            xaxis_title="Presión", yaxis_title="Altura (m)", height=400)
        st.plotly_chart(fig1, use_container_width=True)
    with cg2:
        fig2 = go.Figure(go.Bar(x=["σ techo reducida"], y=[sigma_techo],
            text=[f"{sigma_techo:.4f} MN/m²"], textposition="outside",
            textfont=dict(size=14), marker_color="#388bfd"))
        fig2.update_layout(**PLOTLY_LAYOUT,
            title=dict(text="Soporte Requerido — Techo", font=dict(size=15)),
            height=400, showlegend=False)
        st.plotly_chart(fig2, use_container_width=True)
 
    # ══ BOTÓN GENERAR REPORTE PDF ══
    st.markdown("---")
    st.markdown("### 📄 Exportar Reporte Completo")
    if "selected_design" not in st.session_state:
        st.warning("⚠️ Seleccione un diseño en la pestaña 'Diseños de Mezcla' antes de generar el reporte.")
    else:
        if st.button("📄 Generar Reporte PDF", type="primary", use_container_width=True):
            with st.spinner("Generando reporte PDF, por favor espere..."):
                try:
                    pdf_bytes = build_pdf_report({
                        "selected":   st.session_state.selected_design,
                        "designs_df": st.session_state.designs_df,
                        "resist_df":  st.session_state.resist_df,
                        "espec":      st.session_state.espec,
                        "agre":       st.session_state.agre,
                        "infra":      st.session_state.infra,
                        "geo":        st.session_state.geo,
                        "pendiente":  st.session_state.get("pendiente", -1),
                    })
                    hoy_str = datetime.datetime.now().strftime("%Y%m%d_%H%M")
                    st.download_button(
                        label="⬇️ Descargar PDF",
                        data=pdf_bytes,
                        file_name=f"Reporte_CRF_{st.session_state.selected_design}_{hoy_str}.pdf",
                        mime="application/pdf",
                        use_container_width=True,
                    )
                    st.success("✔ Reporte generado. Haga clic en el botón de descarga.")
                except Exception as e:
                    st.error(f"❌ Error al generar el reporte: {e}")
                    st.exception(e)
 
 
# ─────────────────────────────────────────────
#  FOOTER
# ─────────────────────────────────────────────
hoy = datetime.datetime.now().strftime("%d/%m/%Y")
st.markdown(f"""
<style>
.footer-box {{
    margin-top: 40px; padding: 20px; width: 100%; text-align: center;
    background: rgba(240,165,0,0.10); border-top: 2px solid #f0a500;
    border-radius: 6px; font-size: 14px; color: #e6edf3;
}}
.footer-title {{ font-family: 'Lato', sans-serif; font-size: 16px; color: #f0a500; font-weight: 900; }}
.footer-sub   {{ color: #c9d1d9; font-size: 14px; }}
</style>
<div class="footer-box">
    <div class="footer-title">Cemented Rock Fill · Sistema CRF</div>
    <div class="footer-sub">Aplicación generada para análisis técnico-geomecánico y diseño de relleno detrítico cementado.</div>
    <br>
    <div class="footer-sub">Versión 1.5 · Actualizado el {hoy}</div>
    <div class="footer-sub">Desarrollado por: <b>Bradoc Chambilla</b></div>
</div>
""", unsafe_allow_html=True)

Aquí tienes la Parte 2 + README completo integrado en formato Markdown profesional:

# ⛏️ Cemented Rock Fill (CRF) – Sistema Integral de Diseño Geomecánico y Mezclas

## 📌 Descripción General

Este proyecto es una aplicación avanzada desarrollada en **Streamlit** para el análisis, diseño y simulación de sistemas de **Cemented Rock Fill (CRF)** en minería subterránea, bajo el método **Sub Level Stoping**.

El sistema permite integrar:
- Diseño de mezclas cementadas
- Análisis de resistencia a compresión (MPa)
- Modelado de infraestructura minera
- Cálculos geomecánicos (Mitchell + Dujisin)
- Visualización técnica tipo CAD
- Exportación automática de reportes PDF profesionales

---

# 🧠 Arquitectura General del Sistema

La aplicación está estructurada en 4 módulos principales:

## 🧪 1. Diseños de mezcla
## 📐 2. Diseño seleccionado
## 🏗️ 3. Infraestructura minera
## 📊 4. Geomecánica avanzada

---

# ⚙️ Tecnologías utilizadas

## 🖥️ Framework principal
- Streamlit (interfaz interactiva de ingeniería)

## 📊 Ciencia de datos
- NumPy → Cálculo numérico
- Pandas → Manejo de tablas estructuradas

## 📈 Visualización
- Plotly → Gráficos dinámicos (curvas, barras, pie charts)
- Matplotlib → Diagramas técnicos tipo CAD

## 📄 Reportes técnicos
- ReportLab → Generación de PDF profesional

## 🧮 Cálculo científico
- Math → Geomecánica y trigonometría
- datetime → Trazabilidad de reportes

---

# 💾 Gestión de estado (Session State)

El sistema mantiene datos persistentes en memoria:

- Diseños de mezcla CRF
- Resistencias por edad (7–56 días)
- Especificaciones del concreto
- Propiedades de agregados
- Infraestructura minera
- Parámetros geomecánicos

---

# 📂 Módulo de carga de datos (CSV maestro)

## 📥 Funcionalidades:
- Descarga de plantilla estructurada
- Carga de CSV personalizado
- Parsing automático por secciones
- Restauración de datos por defecto

## 📌 Secciones del CSV:
- DESIGNS → Diseños de mezcla
- RESISTENCIAS → Curvas de resistencia
- ESPECIFICACIONES → Parámetros de diseño
- AGREGADO → Propiedades físicas
- INFRAESTRUCTURA → Equipos y geometría
- GEOMECANICA → Parámetros del macizo rocoso

---

# 🧪 TAB 1 — Diseños de mezcla

## Funciones principales:
- Cálculo de proporciones CRF
- Conversión a porcentajes
- Relación Agua/Cemento (A/C)
- Evaluación de resistencia a 28 días

## Visualizaciones:
- Curvas de resistencia por diseño
- Comparación de barras a 28 días

---

# 📐 TAB 2 — Diseño seleccionado

## Funciones:
- Selección dinámica de diseño CRF
- Balance de materiales
- Ajuste por humedad y absorción
- Cálculo de densidad de mezcla

## Resultados:
- Cemento (kg/m³)
- Agua industrial (L/m³)
- Agregado corregido
- Relación A/C
- Densidad final del CRF

## Visualización:
- Gráfico de torta (composición)
- Gráfico de barras comparativo
- Cálculo por tanda (m³)

---

# 🏗️ TAB 3 — Infraestructura minera

## Funciones:
- Dimensionamiento de cámara de mezclado
- Equipos mineros (bombas, chancadoras, fajas)
- Personal operativo requerido
- Cálculo de volúmenes

## Parámetros:
- Longitud, ancho y altura de cámara
- Espesores estructurales (concreto, shotcrete)
- Pendiente del piso

## Visualización técnica:
- Plano tipo CAD de cámara minera
- LHD integrado (Load-Haul-Dump)
- Hatching técnico de terreno
- Representación geométrica realista

---

# 📊 TAB 4 — Geomecánica avanzada

## Modelos implementados:

### 🧮 Mitchell (1970)
- Presión vertical del macizo
- Coeficiente activo de empuje lateral

### 🧮 Dujisin (1992)
- Reducción por factor de seguridad
- Presión total sobre el caserón

## Variables clave:
- Altura del caserón (H)
- Ángulo de fricción (φ)
- Cohesión del CRF
- Densidad de roca
- Factor de seguridad

## Resultados:
- σ vertical
- σ lateral
- σ techo
- σ total del sistema

## Visualización:
- Distribución de presión lateral
- Comparación Mitchell vs Dujisin
- Barras de soporte estructural

---

# 📄 Generación de Reporte PDF

## Características:
- Reporte técnico automático completo
- Portada profesional
- Tablas estructuradas
- Gráficos incrustados
- Diagramas técnicos (Matplotlib + Plotly)

## Contenido del PDF:
- Diseño de mezcla seleccionado
- Infraestructura minera
- Cálculos geomecánicos
- Curvas de resistencia
- Composición del CRF

---

# 🧾 Interfaz de usuario (UI)

## Diseño:
- Tema oscuro tipo ingeniería minera
- Tipografía Lato
- Colores técnicos:
  - Dorado (#f0a500)
  - Azul (#388bfd)
  - Verde (#3fb950)
  - Rojo (#f85149)

## Componentes:
- Tabs estructurados
- Tablas técnicas estilo ingeniería
- Gráficos interactivos
- Panel de descarga PDF

---

# 📌 Flujo de trabajo del sistema

1. Cargar o usar datos por defecto
2. Seleccionar diseño CRF
3. Analizar resistencia y composición
4. Definir infraestructura minera
5. Evaluar estabilidad geomecánica
6. Generar reporte PDF final

---

# 🚀 Alcance del sistema

Este sistema permite:

✔ Diseño completo de CRF  
✔ Simulación geomecánica avanzada  
✔ Evaluación estructural de cámaras mineras  
✔ Optimización de mezcla cementada  
✔ Reporte técnico automático  
✔ Visualización tipo ingeniería CAD  

---

# 👷 Autor

**Bradoc Chambilla**
