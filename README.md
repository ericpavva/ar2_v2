--[[
    Tetescr hub — UI + AR2 core
    Bloco A: cabeçalho, NL load, paleta, LOCALES (8 idiomas), tradução, persistência
    Idioma padrão: English
]]

-- ============================================================
-- 0. GUARDA DE EXECUÇÃO
-- ============================================================
if getgenv and getgenv().TetescrHUB_Loaded then
    warn("[Tetescr hub] já carregado — use __tetescr_hub_unload() antes de recarregar")
    return
end
if getgenv then getgenv().TetescrHUB_Loaded = true end

-- ============================================================
-- 1. CARREGA A LIBRARY NL
-- ============================================================
local NL
do
    local ok, lib = pcall(function()
        return loadstring(game:HttpGet(
            "https://raw.githubusercontent.com/DeviceHB21/mylib/refs/heads/main/source.lua"
        ))()
    end)
    if not ok or not lib then
        warn("[Tetescr hub] falha ao carregar NL: " .. tostring(lib))
        if getgenv then getgenv().TetescrHUB_Loaded = nil end
        return
    end
    NL = lib
end
if getgenv then getgenv().TetescrHUB_NL = NL end

if NL.ScreenGui then NL.ScreenGui.Enabled = false end

-- ============================================================
-- 2. PALETA
-- ============================================================
ACCENT_DEFAULT = Color3.fromRGB(60, 160, 255)
ROW_BG         = Color3.fromRGB(28, 30, 34)
ROW_BG_HOVER   = Color3.fromRGB(45, 75, 130)
BOX_OFF        = Color3.fromRGB(45, 48, 58)
TEXT_OFF       = Color3.fromRGB(175, 178, 186)
TEXT_ON        = Color3.fromRGB(240, 240, 245)
TEXT_HOVER     = Color3.fromRGB(255, 255, 255)

pcall(function() if NL.SetAccentColor then NL:SetAccentColor(ACCENT_DEFAULT) end end)
NL.AccentColor = ACCENT_DEFAULT

Tween     = game:GetService("TweenService")
TextSvc   = game:GetService("TextService")
UserInput = game:GetService("UserInputService")

-- ============================================================
-- 3. LOCALES — 8 idiomas
-- ============================================================
LOCALES = {}

LOCALES["English"] = {
    -- tabs
    tab_esp = "ESP", tab_combat = "Combat", tab_movement = "Movement",
    tab_cars = "Vehicles", tab_world = "World", tab_radar = "Radar",
    tab_teleport = "Teleport", tab_diversos = "Misc", tab_cos = "Cosmetics",
    tab_config = "Config",

    -- sections
    sec_player_visuals = "Player Visuals", sec_filters = "Filters",
    sec_aim = "Aim", sec_weapon = "Weapon",
    sec_movement_player = "Player", sec_fly = "Fly",
    sec_cars_tuning = "Tuning", sec_cars_noclip = "Noclip",
    sec_lighting = "Lighting", sec_radar_main = "Radar",
    sec_minimap = "Minimap", sec_tp_player = "Player TP",
    sec_freeze = "Freeze", sec_camera = "Camera",
    sec_unlocker = "Unlocker", sec_keybinds = "Keybinds",
    sec_window = "Window", sec_language = "Language",

    -- ESP
    opt_enable_esp = "Enable ESP",
    opt_player_name = "Player Name", opt_player_box = "Player Box",
    opt_player_health = "Player Health", opt_player_distance = "Distance",
    opt_player_skeleton = "Skeleton", opt_player_chams = "Chams",
    opt_player_tracer = "Tracer", opt_player_weapon = "Weapon",
    opt_ignore_teammates = "Ignore Teammates", opt_visible_only = "Visible Only",
    opt_render_distance = "Render Distance",
    opt_zombie_esp = "Zombie ESP", opt_zombie_distance = "Zombie Distance",
    opt_loot_esp = "Loot ESP", opt_loot_distance = "Loot Distance",
    opt_corpse_esp = "Corpse ESP", opt_vehicle_esp = "Vehicle ESP",
    opt_loot_box     = "Loot Box",
    opt_loot_name    = "Loot Name",
    opt_loot_chams   = "Loot Chams",
    opt_corpse_box   = "Corpse Box",
    opt_corpse_name  = "Corpse Name",
    opt_corpse_dist  = "Corpse Distance",
    opt_corpse_only  = "Players Only",
    opt_veh_box      = "Vehicle Box",
    opt_veh_name     = "Vehicle Name",
    opt_veh_chams    = "Vehicle Chams",
    opt_esp_corner_box    = "Corner Box",
    opt_esp_fill          = "Box Fill",
    opt_esp_health_text   = "Health Numbers",
    opt_esp_box_thickness = "Box Thickness",
    opt_esp_text_size     = "Text Size",
    opt_esp_tracer_thick  = "Tracer Thickness",
    opt_esp_tracer_bottom = "Tracer From Bottom",
    opt_esp_color_player  = "Player Color",
    opt_esp_color_armed   = "Armed Player",
    opt_esp_color_zombie  = "Zombie Color",
    opt_esp_color_agro    = "Zombie (Agro)",
    opt_esp_color_vehicle = "Vehicle Color",
    opt_esp_color_info    = "Info Text",

    -- Combat
    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_rb_max_dist        = "Max Distance",
    opt_rb_fov             = "FOV",
    opt_rb_ignore_fov      = "Ignore FOV",
    opt_rb_inventory_shoot = "Shoot from Inventory",
    opt_rb_require_ads     = "Require ADS",
    opt_rb_force_ads       = "Force ADS",
    opt_rb_ads_min_dist    = "ADS Min Distance",
    opt_rb_vis_check       = "Visible Only",
    opt_wall_check = "Wall Check", opt_fov = "FOV",
    opt_smoothing = "Smoothing", opt_no_recoil = "No Recoil",
    opt_no_spread = "No Spread", opt_wallbang = "Wallbang",
    opt_hitmarker = "Hitmarker", opt_instant_reload = "Instant Reload",
    opt_crosshair = "Crosshair",
    opt_hm_shape      = "Shape",
    opt_hm_hue        = "Color Hue",
    opt_hm_size       = "Size",
    opt_hm_thickness  = "Thickness",
    opt_hm_gap        = "Gap",
    opt_hm_opacity    = "Opacity",
    opt_hm_duration   = "Duration (s)",
    opt_hm_rainbow    = "Rainbow",
    opt_ch_shape      = "Shape",
    opt_ch_size       = "Size",
    opt_ch_gap        = "Gap",
    opt_ch_thickness  = "Thickness",
    opt_ch_hue        = "Hue",
    opt_ch_rainbow    = "Rainbow",
    opt_all_fire_modes = "All Fire Modes",
    opt_hbe = "Hitbox Expander", opt_hbe_size = "HBE Size",
    opt_suppressed = "Always Suppressed", opt_veh_equip = "Vehicle Equip",
    opt_max_dist        = "Max Distance",
    opt_target_part     = "Target Part",
    opt_auto_speed      = "Auto Bullet Speed",
    opt_bullet_speed    = "Manual Bullet Speed",
    opt_drop_comp       = "Drop Compensation",
    opt_origin_shift    = "Origin Shift",
    opt_no_teammate     = "Skip Teammates",
    opt_vis_check       = "Visible Only",

    -- Movement
    opt_speed_hack = "Speed Hack", opt_speed_pct = "Speed %",
    opt_infinite_jump = "Infinite Jump", opt_noclip = "Noclip",
    opt_noclip_speed = "Noclip Speed", opt_bunny_hop = "Bunny Hop",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "TP Dash Studs",
    opt_fly = "Fly", opt_fly_speed = "Fly Speed",
    opt_car_fly = "Car Fly", opt_car_fly_speed = "Car Fly Speed",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "Spin Speed",
    opt_speed_collide  = "Collide Check",
    opt_speed_pulse    = "Pulse",
    opt_speed_pulse_on = "Pulse On (s)",
    opt_speed_pulse_off= "Pulse Off (s)",
    opt_speed_adaptive = "Adaptive",
    opt_speed_min_pct  = "Min %",
    opt_speed_airborne = "Airborne",
    opt_speed_spoof    = "Spoof MoveState",
    opt_speed_spoof_as = "Spoof As",
    opt_bhop_height    = "Hop Height",
    opt_spin_tilt      = "Tilt",
    opt_tp_dash_int    = "TP Dash Interval (s)",
    opt_tp_dash_vert   = "TP Dash Vertical",

    -- Cars
    opt_inf_fuel = "Infinite Fuel", opt_boost = "Boost",
    opt_godmode = "Godmode", opt_max_traction = "Max Traction",
    opt_all_cars = "All Cars", opt_car_speed = "Speed",
    opt_car_torque = "Torque", opt_vehicle_noclip = "Vehicle Noclip",
    opt_noclip_speed_car = "Noclip Speed",
    opt_boat_mode = "Boat Mode", opt_remove_drag = "Remove Drag",
    opt_full_steer = "Full Steer", opt_car_tp_dash = "Car TP Dash",
    opt_car_tp_studs   = "Car Dash Studs",
    opt_car_tp_int     = "Car Dash Interval (s)",
    opt_car_tp_vert    = "Car Dash Vertical",
    opt_noclip_tp_speed = "Noclip TP Speed",
    opt_noclip_tp_vert  = "Noclip TP Vertical",
    opt_noclip_tp_max   = "Noclip TP Max Time (s)",
    opt_noclip_eject    = "Noclip Eject Delay (s)",

    -- World
    opt_fullbright = "Fullbright", opt_no_fog = "No Fog",
    opt_brightness = "Brightness", opt_time = "Time",
    opt_remove_shadows = "Remove Shadows",
    opt_remove_textures = "Remove Textures", opt_remove_leaves = "Remove Leaves",
    opt_remove_bushes = "Remove Bushes",
    opt_fov_zoom = "FOV Zoom (hold RMB)", opt_zoom_fov = "Zoom FOV",
    opt_dist_zoom = "Distance Zoom", opt_normal_dist = "Normal Dist",
    opt_zoomed_dist = "Zoomed Dist", opt_3p_ads = "Third Person ADS",
    opt_remove_sunrays = "Remove Sun Rays", opt_custom_ambient = "Custom Ambient",
    sec_skybox        = "Skybox",
    sec_clouds        = "Clouds",
    sec_cc            = "Color Correction",
    opt_skybox        = "Skybox Preset",
    opt_clouds_on     = "Enable Clouds",
    opt_clouds_modify = "Modify Clouds",
    opt_clouds_color  = "Cloud Color",
    opt_clouds_cover  = "Cloud Cover",
    opt_clouds_density= "Cloud Density",
    opt_cc_on         = "Enable CC",
    opt_cc_sat        = "Saturation",
    opt_cc_cont       = "Contrast",
    opt_cc_bright     = "Brightness",

    -- Radar
    opt_radar_enabled = "Enabled", opt_radar_names = "Names",
    opt_radar_dist = "Distance", opt_radar_self = "Include Self",
    opt_radar_armed = "Armed Color",
    opt_minimap = "Minimap", opt_minimap_size = "Minimap Size",
    opt_radar_vehicles = "Radar Vehicles", opt_radar_corpses = "Radar Corpses",
    opt_radar_weapon = "Show Weapon",
    opt_minimap_x    = "Minimap X",
    opt_minimap_y    = "Minimap Y",

    -- Teleport
    opt_behind = "Behind", opt_above = "Above", opt_side = "Side",
    opt_tp_to_target = "TP To Target", opt_open_map = "Open Map",
    opt_respawn_tp = "Respawn TP", opt_target = "Target",

    -- Diversos
    opt_freeze_zombies = "Freeze Zombies", opt_whole_server = "Whole Server",
    opt_freeze_anchor = "Anchor", opt_radius = "Radius",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "Jump Circle",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "Instant Interact",
    opt_player_jesus = "Player Jesus", opt_zombie_circle = "Zombie Circle",
    opt_hide_player = "Hide Player", opt_hide_gun = "Hide Gun",
    opt_anti_fall = "Anti-Fall Stun",
    sec_outlines      = "Player Outlines",
    sec_personal      = "Personal Appearance",
    opt_charm_mode    = "Mode",
    opt_charm_hue     = "Hue",
    opt_charm_gray    = "Gray",
    opt_charm_rainbow = "Rainbow",
    opt_charm_global  = "Global Outline",
    opt_pc_char_on    = "Character Appearance",
    opt_pc_char_mode  = "Char Mode",
    opt_pc_char_hue   = "Char Hue",
    opt_pc_char_gray  = "Char Gray",
    opt_pc_char_mat   = "Char Material",
    opt_pc_char_rb    = "Char Rainbow",
    opt_pg_gun_on     = "Gun Appearance",
    opt_pg_gun_mode   = "Gun Mode",
    opt_pg_gun_hue    = "Gun Hue",
    opt_pg_gun_gray   = "Gun Gray",
    opt_pg_gun_mat    = "Gun Material",
    opt_pg_gun_rb     = "Gun Rainbow",

    -- Cosmetics
    opt_enable_selection = "Enable Selection",
    opt_remove_all_cos = "Remove All Cosmetics",

    -- Config / janela
    opt_save_config = "Save Config", opt_load_config = "Load Config",
    opt_reset_defaults = "Reset Defaults",
    opt_minimize = "Minimize UI", opt_toggle_ui = "Toggle UI",
    opt_language = "Language",
    opt_config_name = "Config name",
    opt_toggle_ui_key = "Open/Close UI",

            opt_show_fov = "Show FOV",
        opt_show_lock = "Show Lock Line",
        opt_ignore_fov = "Ignore FOV",
        opt_silent_fov = "Silent Aim FOV",
        opt_max_bend = "Max Bend",
        opt_redirect = "Redirect %",
        opt_kill_spread = "Kill Spread",
        opt_fov_aimbot = "FOV Aimbot",
        opt_target_players = "Target Players",
        opt_target_zombies = "Target Zombies",
        opt_recoil_reduction = "Recoil Reduction %",
        opt_auto_reload      = "Auto Reload",
}

LOCALES["Português"] = {
    tab_esp = "ESP", tab_combat = "Combate", tab_movement = "Movimento",
    tab_cars = "Veículos", tab_world = "Mundo", tab_radar = "Radar",
    tab_teleport = "Teleporte", tab_diversos = "Diversos", tab_cos = "Cosméticos",
    tab_config = "Config",

    sec_player_visuals = "Visual do Jogador", sec_filters = "Filtros",
    sec_aim = "Mira", sec_weapon = "Arma",
    sec_movement_player = "Jogador", sec_fly = "Voo",
    sec_cars_tuning = "Ajustes", sec_cars_noclip = "No Clip",
    sec_lighting = "Iluminação", sec_radar_main = "Radar",
    sec_minimap = "Minimapa", sec_tp_player = "TP Jogador",
    sec_freeze = "Congelar", sec_camera = "Câmera",
    sec_unlocker = "Desbloqueador", sec_keybinds = "Teclas",
    sec_window = "Janela", sec_language = "Idioma",

    opt_enable_esp = "Ativar ESP",
    opt_player_name = "Nome do Jogador", opt_player_box = "Caixa do Jogador",
    opt_player_health = "Vida do Jogador", opt_player_distance = "Distância",
    opt_player_skeleton = "Esqueleto", opt_player_chams = "Chams",
    opt_player_tracer = "Tracer", opt_player_weapon = "Arma",
    opt_ignore_teammates = "Ignorar Aliados", opt_visible_only = "Só Visíveis",
    opt_render_distance = "Distância de Renderização",
    opt_zombie_esp = "ESP de Zumbi", opt_zombie_distance = "Distância do Zumbi",
    opt_loot_esp = "ESP de Loot", opt_loot_distance = "Distância do Loot",
    opt_corpse_esp = "ESP de Corpo", opt_vehicle_esp = "ESP de Veículo",

    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_wall_check = "Checar Parede", opt_fov = "FOV",
    opt_smoothing = "Suavização", opt_no_recoil = "Sem Recuo",
    opt_no_spread = "Sem Dispersão", opt_wallbang = "Wallbang",
    opt_hitmarker = "Hitmarker", opt_instant_reload = "Recarga Instantânea",
    opt_all_fire_modes = "Todos os Modos de Tiro",
    opt_hbe = "Expansor de Hitbox", opt_hbe_size = "Tamanho do HBE",
    opt_suppressed = "Sempre Suprimido", opt_veh_equip = "Equipar em Veículo",

    opt_speed_hack = "Velocidade", opt_speed_pct = "Velocidade %",
    opt_infinite_jump = "Pulo Infinito", opt_noclip = "Noclip",
    opt_noclip_speed = "Velocidade do Noclip", opt_bunny_hop = "Bunny Hop",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "Studs do TP Dash",
    opt_fly = "Voar", opt_fly_speed = "Velocidade do Voo",
    opt_car_fly = "Carro Voador", opt_car_fly_speed = "Velocidade do Carro Voador",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "Velocidade do Spin",

    opt_inf_fuel = "Combustível Infinito", opt_boost = "Turbo",
    opt_godmode = "Deus", opt_max_traction = "Tração Máxima",
    opt_all_cars = "Todos os Carros", opt_car_speed = "Velocidade",
    opt_car_torque = "Torque", opt_vehicle_noclip = "Noclip do Veículo",
    opt_noclip_speed_car = "Velocidade do Noclip",
    opt_boat_mode = "Modo Barco", opt_remove_drag = "Remover Arrasto",
    opt_full_steer = "Direção Total", opt_car_tp_dash = "TP Dash do Carro",

    opt_fullbright = "Iluminação Total", opt_no_fog = "Sem Névoa",
    opt_brightness = "Brilho", opt_time = "Hora",
    opt_remove_shadows = "Remover Sombras",
    opt_remove_textures = "Remover Texturas", opt_remove_leaves = "Remover Folhas",
    opt_remove_bushes = "Remover Arbustos",
    opt_fov_zoom = "Zoom de FOV (segurar RMB)", opt_zoom_fov = "FOV do Zoom",
    opt_dist_zoom = "Zoom de Distância", opt_normal_dist = "Dist. Normal",
    opt_zoomed_dist = "Dist. com Zoom", opt_3p_ads = "ADS em 3ª Pessoa",
    opt_remove_sunrays = "Remover Raios de Sol", opt_custom_ambient = "Ambiente Custom",

    opt_radar_enabled = "Ativado", opt_radar_names = "Nomes",
    opt_radar_dist = "Distância", opt_radar_self = "Incluir a Si",
    opt_radar_armed = "Cor Armado",
    opt_minimap = "Minimapa", opt_minimap_size = "Tamanho do Minimapa",
    opt_radar_vehicles = "Veículos no Radar", opt_radar_corpses = "Corpos no Radar",

    opt_behind = "Atrás", opt_above = "Acima", opt_side = "Ao Lado",
    opt_tp_to_target = "TP para o Alvo", opt_open_map = "Abrir Mapa",
    opt_respawn_tp = "TP ao Renascer", opt_target = "Alvo",

    opt_freeze_zombies = "Congelar Zumbis", opt_whole_server = "Servidor Todo",
    opt_freeze_anchor = "Ancorar", opt_radius = "Raio",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "Círculo de Pulo",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "Interação Instantânea",
    opt_player_jesus = "Jesus do Jogador", opt_zombie_circle = "Círculo de Zumbis",
    opt_hide_player = "Esconder Jogador", opt_hide_gun = "Esconder Arma",
    opt_anti_fall = "Anti-Queda",

    opt_enable_selection = "Ativar Seleção",
    opt_remove_all_cos = "Remover Todos os Cosméticos",

    opt_save_config = "Salvar Config", opt_load_config = "Carregar Config",
    opt_reset_defaults = "Resetar Padrões",
    opt_minimize = "Minimizar UI", opt_toggle_ui = "Abrir/Fechar UI",
    opt_language = "Idioma",
    opt_config_name = "Nome da config",
    opt_toggle_ui_key = "Abrir/Fechar UI",

            opt_show_fov = "Mostrar FOV",
        opt_show_lock = "Mostrar Linha de Lock",
        opt_ignore_fov = "Ignorar FOV",
        opt_silent_fov = "FOV do Silent Aim",
        opt_max_bend = "Ângulo Máximo",
        opt_redirect = "Chance de Redirecionar %",
        opt_kill_spread = "Sem Dispersão no Alvo",
        opt_fov_aimbot = "FOV do Aimbot",
                opt_target_players = "Mirar em Jogadores",
        opt_target_zombies = "Mirar em Zumbis",
}

LOCALES["Español"] = {
    tab_esp = "ESP", tab_combat = "Combate", tab_movement = "Movimiento",
    tab_cars = "Vehículos", tab_world = "Mundo", tab_radar = "Radar",
    tab_teleport = "Teletransporte", tab_diversos = "Varios", tab_cos = "Cosméticos",
    tab_config = "Config",

    sec_player_visuals = "Visual del Jugador", sec_filters = "Filtros",
    sec_aim = "Apuntado", sec_weapon = "Arma",
    sec_movement_player = "Jugador", sec_fly = "Vuelo",
    sec_cars_tuning = "Ajustes", sec_cars_noclip = "No Clip",
    sec_lighting = "Iluminación", sec_radar_main = "Radar",
    sec_minimap = "Minimapa", sec_tp_player = "TP Jugador",
    sec_freeze = "Congelar", sec_camera = "Cámara",
    sec_unlocker = "Desbloqueador", sec_keybinds = "Teclas",
    sec_window = "Ventana", sec_language = "Idioma",

    opt_enable_esp = "Activar ESP",
    opt_player_name = "Nombre del Jugador", opt_player_box = "Caja del Jugador",
    opt_player_health = "Vida del Jugador", opt_player_distance = "Distancia",
    opt_player_skeleton = "Esqueleto", opt_player_chams = "Chams",
    opt_player_tracer = "Tracer", opt_player_weapon = "Arma",
    opt_ignore_teammates = "Ignorar Aliados", opt_visible_only = "Solo Visibles",
    opt_render_distance = "Distancia de Render",
    opt_zombie_esp = "ESP de Zombi", opt_zombie_distance = "Distancia del Zombi",
    opt_loot_esp = "ESP de Loot", opt_loot_distance = "Distancia del Loot",
    opt_corpse_esp = "ESP de Cadáver", opt_vehicle_esp = "ESP de Vehículo",

    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_wall_check = "Comprobar Pared", opt_fov = "FOV",
    opt_smoothing = "Suavizado", opt_no_recoil = "Sin Retroceso",
    opt_no_spread = "Sin Dispersión", opt_wallbang = "Wallbang",
    opt_hitmarker = "Hitmarker", opt_instant_reload = "Recarga Instantánea",
    opt_all_fire_modes = "Todos los Modos de Disparo",
    opt_hbe = "Expansor de Hitbox", opt_hbe_size = "Tamaño del HBE",
    opt_suppressed = "Siempre Suprimido", opt_veh_equip = "Equipar en Vehículo",

    opt_speed_hack = "Velocidad", opt_speed_pct = "Velocidad %",
    opt_infinite_jump = "Salto Infinito", opt_noclip = "Noclip",
    opt_noclip_speed = "Velocidad del Noclip", opt_bunny_hop = "Bunny Hop",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "Studs del TP Dash",
    opt_fly = "Volar", opt_fly_speed = "Velocidad de Vuelo",
    opt_car_fly = "Coche Volador", opt_car_fly_speed = "Velocidad del Coche Volador",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "Velocidad del Spin",

    opt_inf_fuel = "Combustible Infinito", opt_boost = "Turbo",
    opt_godmode = "Modo Dios", opt_max_traction = "Tracción Máxima",
    opt_all_cars = "Todos los Coches", opt_car_speed = "Velocidad",
    opt_car_torque = "Torque", opt_vehicle_noclip = "Noclip del Vehículo",
    opt_noclip_speed_car = "Velocidad del Noclip",
    opt_boat_mode = "Modo Barco", opt_remove_drag = "Quitar Arrastre",
    opt_full_steer = "Dirección Total", opt_car_tp_dash = "TP Dash del Coche",

    opt_fullbright = "Luz Total", opt_no_fog = "Sin Niebla",
    opt_brightness = "Brillo", opt_time = "Hora",
    opt_remove_shadows = "Quitar Sombras",
    opt_remove_textures = "Quitar Texturas", opt_remove_leaves = "Quitar Hojas",
    opt_remove_bushes = "Quitar Arbustos",
    opt_fov_zoom = "Zoom de FOV (mantener RMB)", opt_zoom_fov = "FOV del Zoom",
    opt_dist_zoom = "Zoom de Distancia", opt_normal_dist = "Dist. Normal",
    opt_zoomed_dist = "Dist. con Zoom", opt_3p_ads = "ADS en 3ª Persona",
    opt_remove_sunrays = "Quitar Rayos de Sol", opt_custom_ambient = "Ambiente Custom",

    opt_radar_enabled = "Activado", opt_radar_names = "Nombres",
    opt_radar_dist = "Distancia", opt_radar_self = "Incluirse",
    opt_radar_armed = "Color Armado",
    opt_minimap = "Minimapa", opt_minimap_size = "Tamaño del Minimapa",
    opt_radar_vehicles = "Vehículos en Radar", opt_radar_corpses = "Cadáveres en Radar",

    opt_behind = "Detrás", opt_above = "Arriba", opt_side = "Al Lado",
    opt_tp_to_target = "TP al Objetivo", opt_open_map = "Abrir Mapa",
    opt_respawn_tp = "TP al Renacer", opt_target = "Objetivo",

    opt_freeze_zombies = "Congelar Zombis", opt_whole_server = "Todo el Servidor",
    opt_freeze_anchor = "Anclar", opt_radius = "Radio",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "Círculo de Salto",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "Interacción Instantánea",
    opt_player_jesus = "Jesús del Jugador", opt_zombie_circle = "Círculo de Zombis",
    opt_hide_player = "Ocultar Jugador", opt_hide_gun = "Ocultar Arma",
    opt_anti_fall = "Anti-Caída",

    opt_enable_selection = "Activar Selección",
    opt_remove_all_cos = "Quitar Todos los Cosméticos",

    opt_save_config = "Guardar Config", opt_load_config = "Cargar Config",
    opt_reset_defaults = "Restaurar Por Defecto",
    opt_minimize = "Minimizar UI", opt_toggle_ui = "Abrir/Cerrar UI",
    opt_language = "Idioma",
    opt_config_name = "Nombre de config",
    opt_toggle_ui_key = "Abrir/Cerrar UI",
}

LOCALES["Français"] = {
    tab_esp = "ESP", tab_combat = "Combat", tab_movement = "Mouvement",
    tab_cars = "Véhicules", tab_world = "Monde", tab_radar = "Radar",
    tab_teleport = "Téléportation", tab_diversos = "Divers", tab_cos = "Cosmétiques",
    tab_config = "Config",

    sec_player_visuals = "Visuel du Joueur", sec_filters = "Filtres",
    sec_aim = "Visée", sec_weapon = "Arme",
    sec_movement_player = "Joueur", sec_fly = "Vol",
    sec_cars_tuning = "Réglages", sec_cars_noclip = "No Clip",
    sec_lighting = "Éclairage", sec_radar_main = "Radar",
    sec_minimap = "Mini-carte", sec_tp_player = "TP Joueur",
    sec_freeze = "Geler", sec_camera = "Caméra",
    sec_unlocker = "Débloqueur", sec_keybinds = "Touches",
    sec_window = "Fenêtre", sec_language = "Langue",

    opt_enable_esp = "Activer ESP",
    opt_player_name = "Nom du Joueur", opt_player_box = "Boîte du Joueur",
    opt_player_health = "Vie du Joueur", opt_player_distance = "Distance",
    opt_player_skeleton = "Squelette", opt_player_chams = "Chams",
    opt_player_tracer = "Tracer", opt_player_weapon = "Arme",
    opt_ignore_teammates = "Ignorer Alliés", opt_visible_only = "Visibles Seulement",
    opt_render_distance = "Distance d'Affichage",
    opt_zombie_esp = "ESP Zombie", opt_zombie_distance = "Distance Zombie",
    opt_loot_esp = "ESP Butin", opt_loot_distance = "Distance Butin",
    opt_corpse_esp = "ESP Cadavre", opt_vehicle_esp = "ESP Véhicule",

    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_wall_check = "Vérifier Mur", opt_fov = "FOV",
    opt_smoothing = "Lissage", opt_no_recoil = "Sans Recul",
    opt_no_spread = "Sans Dispersion", opt_wallbang = "Wallbang",
    opt_hitmarker = "Hitmarker", opt_instant_reload = "Recharge Instantanée",
    opt_all_fire_modes = "Tous les Modes de Tir",
    opt_hbe = "Extension de Hitbox", opt_hbe_size = "Taille HBE",
    opt_suppressed = "Toujours Supprimé", opt_veh_equip = "Équiper en Véhicule",

    opt_speed_hack = "Vitesse", opt_speed_pct = "Vitesse %",
    opt_infinite_jump = "Saut Infini", opt_noclip = "Noclip",
    opt_noclip_speed = "Vitesse Noclip", opt_bunny_hop = "Bunny Hop",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "Studs du TP Dash",
    opt_fly = "Voler", opt_fly_speed = "Vitesse de Vol",
    opt_car_fly = "Voiture Volante", opt_car_fly_speed = "Vitesse Voiture Volante",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "Vitesse du Spin",

    opt_inf_fuel = "Carburant Infini", opt_boost = "Turbo",
    opt_godmode = "Mode Dieu", opt_max_traction = "Traction Maximale",
    opt_all_cars = "Toutes les Voitures", opt_car_speed = "Vitesse",
    opt_car_torque = "Couple", opt_vehicle_noclip = "Noclip du Véhicule",
    opt_noclip_speed_car = "Vitesse Noclip",
    opt_boat_mode = "Mode Bateau", opt_remove_drag = "Retirer Traînée",
    opt_full_steer = "Direction Totale", opt_car_tp_dash = "TP Dash Voiture",

    opt_fullbright = "Pleine Lumière", opt_no_fog = "Sans Brouillard",
    opt_brightness = "Luminosité", opt_time = "Heure",
    opt_remove_shadows = "Retirer Ombres",
    opt_remove_textures = "Retirer Textures", opt_remove_leaves = "Retirer Feuilles",
    opt_remove_bushes = "Retirer Buissons",
    opt_fov_zoom = "Zoom FOV (maintenir RMB)", opt_zoom_fov = "FOV du Zoom",
    opt_dist_zoom = "Zoom de Distance", opt_normal_dist = "Dist. Normale",
    opt_zoomed_dist = "Dist. Zoomée", opt_3p_ads = "ADS en 3ème Personne",
    opt_remove_sunrays = "Retirer Rayons de Soleil", opt_custom_ambient = "Ambiance Custom",

    opt_radar_enabled = "Activé", opt_radar_names = "Noms",
    opt_radar_dist = "Distance", opt_radar_self = "S'Inclure",
    opt_radar_armed = "Couleur Armé",
    opt_minimap = "Mini-carte", opt_minimap_size = "Taille Mini-carte",
    opt_radar_vehicles = "Véhicules Radar", opt_radar_corpses = "Cadavres Radar",

    opt_behind = "Derrière", opt_above = "Dessus", opt_side = "À Côté",
    opt_tp_to_target = "TP vers la Cible", opt_open_map = "Ouvrir la Carte",
    opt_respawn_tp = "TP au Respawn", opt_target = "Cible",

    opt_freeze_zombies = "Geler les Zombies", opt_whole_server = "Tout le Serveur",
    opt_freeze_anchor = "Ancrer", opt_radius = "Rayon",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "Cercle de Saut",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "Interaction Instantanée",
    opt_player_jesus = "Jésus du Joueur", opt_zombie_circle = "Cercle de Zombies",
    opt_hide_player = "Cacher Joueur", opt_hide_gun = "Cacher Arme",
    opt_anti_fall = "Anti-Chute",

    opt_enable_selection = "Activer la Sélection",
    opt_remove_all_cos = "Retirer Tous les Cosmétiques",

    opt_save_config = "Sauvegarder", opt_load_config = "Charger Config",
    opt_reset_defaults = "Réinitialiser",
    opt_minimize = "Minimiser UI", opt_toggle_ui = "Ouvrir/Fermer UI",
    opt_language = "Langue",
    opt_config_name = "Nom de config",
    opt_toggle_ui_key = "Ouvrir/Fermer UI",
}

LOCALES["Deutsch"] = {
    tab_esp = "ESP", tab_combat = "Kampf", tab_movement = "Bewegung",
    tab_cars = "Fahrzeuge", tab_world = "Welt", tab_radar = "Radar",
    tab_teleport = "Teleport", tab_diversos = "Verschiedenes", tab_cos = "Kosmetik",
    tab_config = "Config",

    sec_player_visuals = "Spieler-Anzeige", sec_filters = "Filter",
    sec_aim = "Zielen", sec_weapon = "Waffe",
    sec_movement_player = "Spieler", sec_fly = "Fliegen",
    sec_cars_tuning = "Feinabstimmung", sec_cars_noclip = "No Clip",
    sec_lighting = "Beleuchtung", sec_radar_main = "Radar",
    sec_minimap = "Minikarte", sec_tp_player = "TP Spieler",
    sec_freeze = "Einfrieren", sec_camera = "Kamera",
    sec_unlocker = "Freischalter", sec_keybinds = "Tasten",
    sec_window = "Fenster", sec_language = "Sprache",

    opt_enable_esp = "ESP Aktivieren",
    opt_player_name = "Spielername", opt_player_box = "Spieler-Box",
    opt_player_health = "Spieler-Leben", opt_player_distance = "Distanz",
    opt_player_skeleton = "Skelett", opt_player_chams = "Chams",
    opt_player_tracer = "Tracer", opt_player_weapon = "Waffe",
    opt_ignore_teammates = "Teammates Ignorieren", opt_visible_only = "Nur Sichtbare",
    opt_render_distance = "Sichtweite",
    opt_zombie_esp = "Zombie ESP", opt_zombie_distance = "Zombie Distanz",
    opt_loot_esp = "Loot ESP", opt_loot_distance = "Loot Distanz",
    opt_corpse_esp = "Leichen ESP", opt_vehicle_esp = "Fahrzeug ESP",

    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_wall_check = "Wand-Check", opt_fov = "FOV",
    opt_smoothing = "Glättung", opt_no_recoil = "Kein Rückstoß",
    opt_no_spread = "Keine Streuung", opt_wallbang = "Wallbang",
    opt_hitmarker = "Hitmarker", opt_instant_reload = "Sofort Nachladen",
    opt_all_fire_modes = "Alle Feuer Modi",
    opt_hbe = "Hitbox Erweiterer", opt_hbe_size = "HBE Größe",
    opt_suppressed = "Immer Unterdrückt", opt_veh_equip = "Im Fahrzeug Ausrüsten",

    opt_speed_hack = "Geschwindigkeit", opt_speed_pct = "Geschw. %",
    opt_infinite_jump = "Unendlich Springen", opt_noclip = "Noclip",
    opt_noclip_speed = "Noclip Geschwindigkeit", opt_bunny_hop = "Bunny Hop",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "TP Dash Studs",
    opt_fly = "Fliegen", opt_fly_speed = "Fluggeschwindigkeit",
    opt_car_fly = "Auto Fliegen", opt_car_fly_speed = "Auto Fluggeschw.",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "Spin Geschw.",

    opt_inf_fuel = "Unendl. Treibstoff", opt_boost = "Turbo",
    opt_godmode = "Gottmodus", opt_max_traction = "Max Traktion",
    opt_all_cars = "Alle Autos", opt_car_speed = "Geschwindigkeit",
    opt_car_torque = "Drehmoment", opt_vehicle_noclip = "Fahrzeug Noclip",
    opt_noclip_speed_car = "Noclip-Geschw.",
    opt_boat_mode = "Boot Modus", opt_remove_drag = "Luftwiderstand Entfernen",
    opt_full_steer = "Volle Lenkung", opt_car_tp_dash = "Auto TP Dash",

    opt_fullbright = "Volle Helligkeit", opt_no_fog = "Kein Nebel",
    opt_brightness = "Helligkeit", opt_time = "Uhrzeit",
    opt_remove_shadows = "Schatten Entfernen",
    opt_remove_textures = "Texturen Entfernen", opt_remove_leaves = "Blätter Entfernen",
    opt_remove_bushes = "Büsche Entfernen",
    opt_fov_zoom = "FOV-Zoom (RMB halten)", opt_zoom_fov = "Zoom FOV",
    opt_dist_zoom = "Distanz-Zoom", opt_normal_dist = "Normale Dist.",
    opt_zoomed_dist = "Gezoomte Dist.", opt_3p_ads = "3. Person ADS",
    opt_remove_sunrays = "Sonnenstrahlen Entfernen", opt_custom_ambient = "Custom Ambiente",

    opt_radar_enabled = "Aktiviert", opt_radar_names = "Namen",
    opt_radar_dist = "Distanz", opt_radar_self = "Selbst Einbeziehen",
    opt_radar_armed = "Bewaffnet Farbe",
    opt_minimap = "Minikarte", opt_minimap_size = "Minikarten Größe",
    opt_radar_vehicles = "Fahrzeuge im Radar", opt_radar_corpses = "Leichen im Radar",

    opt_behind = "Dahinter", opt_above = "Darüber", opt_side = "Daneben",
    opt_tp_to_target = "TP zum Ziel", opt_open_map = "Karte Öffnen",
    opt_respawn_tp = "TP beim Respawn", opt_target = "Ziel",

    opt_freeze_zombies = "Zombies Einfrieren", opt_whole_server = "Ganzer Server",
    opt_freeze_anchor = "Verankern", opt_radius = "Radius",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "Sprung-Kreis",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "Sofort Interaktion",
    opt_player_jesus = "Spieler Jesus", opt_zombie_circle = "Zombie-Kreis",
    opt_hide_player = "Spieler Verstecken", opt_hide_gun = "Waffe Verstecken",
    opt_anti_fall = "Anti-Fall Stun",

    opt_enable_selection = "Auswahl Aktivieren",
    opt_remove_all_cos = "Alle Kosmetik Entfernen",

    opt_save_config = "Speichern", opt_load_config = "Laden",
    opt_reset_defaults = "Standard Wiederherstellen",
    opt_minimize = "UI Minimieren", opt_toggle_ui = "UI Ein-/Ausblenden",
    opt_language = "Sprache",
    opt_config_name = "Config Name",
    opt_toggle_ui_key = "UI Ein-/Ausblenden",
}

LOCALES["Русский"] = {
    tab_esp = "ESP", tab_combat = "Бой", tab_movement = "Движение",
    tab_cars = "Машины", tab_world = "Мир", tab_radar = "Радар",
    tab_teleport = "Телепорт", tab_diversos = "Разное", tab_cos = "Косметика",
    tab_config = "Настройки",

    sec_player_visuals = "Отображение Игрока", sec_filters = "Фильтры",
    sec_aim = "Прицел", sec_weapon = "Оружие",
    sec_movement_player = "Игрок", sec_fly = "Полёт",
    sec_cars_tuning = "Настройка", sec_cars_noclip = "No Clip",
    sec_lighting = "Освещение", sec_radar_main = "Радар",
    sec_minimap = "Мини-карта", sec_tp_player = "TP Игрока",
    sec_freeze = "Заморозка", sec_camera = "Камера",
    sec_unlocker = "Разблокировка", sec_keybinds = "Клавиши",
    sec_window = "Окно", sec_language = "Язык",

    opt_enable_esp = "Включить ESP",
    opt_player_name = "Имя Игрока", opt_player_box = "Бокс Игрока",
    opt_player_health = "Здоровье", opt_player_distance = "Дистанция",
    opt_player_skeleton = "Скелет", opt_player_chams = "Chams",
    opt_player_tracer = "Tracer", opt_player_weapon = "Оружие",
    opt_ignore_teammates = "Игнорировать Союзников", opt_visible_only = "Только Видимые",
    opt_render_distance = "Дистанция Отрисовки",
    opt_zombie_esp = "ESP Зомби", opt_zombie_distance = "Дистанция Зомби",
    opt_loot_esp = "ESP Лута", opt_loot_distance = "Дистанция Лута",
    opt_corpse_esp = "ESP Трупа", opt_vehicle_esp = "ESP Машины",

    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_wall_check = "Проверка Стены", opt_fov = "FOV",
    opt_smoothing = "Сглаживание", opt_no_recoil = "Без Отдачи",
    opt_no_spread = "Без Разброса", opt_wallbang = "Wallbang",
    opt_hitmarker = "Hitmarker", opt_instant_reload = "Мгновенная Перезарядка",
    opt_all_fire_modes = "Все Режимы Огня",
    opt_hbe = "Расширитель Хитбокса", opt_hbe_size = "Размер HBE",
    opt_suppressed = "Всегда Подавлен", opt_veh_equip = "Экипировка в Машине",

    opt_speed_hack = "Скорость", opt_speed_pct = "Скорость %",
    opt_infinite_jump = "Бесконечный Прыжок", opt_noclip = "Noclip",
    opt_noclip_speed = "Скорость Noclip", opt_bunny_hop = "Bunny Hop",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "Studs TP Dash",
    opt_fly = "Полёт", opt_fly_speed = "Скорость Полёта",
    opt_car_fly = "Летающая Машина", opt_car_fly_speed = "Скорость Летающей Машины",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "Скорость Спина",

    opt_inf_fuel = "Бесконечное Топливо", opt_boost = "Турбо",
    opt_godmode = "Бог", opt_max_traction = "Макс Сцепление",
    opt_all_cars = "Все Машины", opt_car_speed = "Скорость",
    opt_car_torque = "Крутящий Момент", opt_vehicle_noclip = "Noclip Машины",
    opt_noclip_speed_car = "Скорость Noclip",
    opt_boat_mode = "Режим Лодки", opt_remove_drag = "Убрать Сопротивление",
    opt_full_steer = "Полное Управление", opt_car_tp_dash = "TP Dash Машины",

    opt_fullbright = "Яркое Освещение", opt_no_fog = "Без Тумана",
    opt_brightness = "Яркость", opt_time = "Время",
    opt_remove_shadows = "Убрать Тени",
    opt_remove_textures = "Убрать Текстуры", opt_remove_leaves = "Убрать Листья",
    opt_remove_bushes = "Убрать Кусты",
    opt_fov_zoom = "FOV Зум (держать RMB)", opt_zoom_fov = "FOV Зума",
    opt_dist_zoom = "Зум Дистанции", opt_normal_dist = "Обычная Дист.",
    opt_zoomed_dist = "Дист. Зума", opt_3p_ads = "ADS 3-е Лицо",
    opt_remove_sunrays = "Убрать Лучи Солнца", opt_custom_ambient = "Custom Освещение",

    opt_radar_enabled = "Включено", opt_radar_names = "Имена",
    opt_radar_dist = "Дистанция", opt_radar_self = "Включить Себя",
    opt_radar_armed = "Цвет Вооружён",
    opt_minimap = "Мини-карта", opt_minimap_size = "Размер Мини-карты",
    opt_radar_vehicles = "Машины на Радаре", opt_radar_corpses = "Трупы на Радаре",

    opt_behind = "Сзади", opt_above = "Сверху", opt_side = "Сбоку",
    opt_tp_to_target = "TP к Цели", opt_open_map = "Открыть Карту",
    opt_respawn_tp = "TP при Респавне", opt_target = "Цель",

    opt_freeze_zombies = "Заморозить Зомби", opt_whole_server = "Весь Сервер",
    opt_freeze_anchor = "Закрепить", opt_radius = "Радиус",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "Круг Прыжка",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "Мгновенное Взаимодействие",
    opt_player_jesus = "Игрок Иисус", opt_zombie_circle = "Круг Зомби",
    opt_hide_player = "Скрыть Игрока", opt_hide_gun = "Скрыть Оружие",
    opt_anti_fall = "Anti-Fall Stun",

    opt_enable_selection = "Включить Выбор",
    opt_remove_all_cos = "Убрать Всю Косметику",

    opt_save_config = "Сохранить", opt_load_config = "Загрузить",
    opt_reset_defaults = "Сброс по Умолчанию",
    opt_minimize = "Свернуть UI", opt_toggle_ui = "Показать/Скрыть UI",
    opt_language = "Язык",
    opt_config_name = "Имя конфига",
    opt_toggle_ui_key = "Показать/Скрыть UI",
}

LOCALES["中文"] = {
    tab_esp = "ESP", tab_combat = "战斗", tab_movement = "移动",
    tab_cars = "载具", tab_world = "世界", tab_radar = "雷达",
    tab_teleport = "传送", tab_diversos = "杂项", tab_cos = "外观",
    tab_config = "设置",

    sec_player_visuals = "玩家视觉", sec_filters = "过滤器",
    sec_aim = "瞄准", sec_weapon = "武器",
    sec_movement_player = "玩家", sec_fly = "飞行",
    sec_cars_tuning = "调校", sec_cars_noclip = "穿墙",
    sec_lighting = "光照", sec_radar_main = "雷达",
    sec_minimap = "小地图", sec_tp_player = "玩家传送",
    sec_freeze = "冻结", sec_camera = "相机",
    sec_unlocker = "解锁", sec_keybinds = "快捷键",
    sec_window = "窗口", sec_language = "语言",

    opt_enable_esp = "启用 ESP",
    opt_player_name = "玩家名称", opt_player_box = "玩家方框",
    opt_player_health = "玩家血量", opt_player_distance = "距离",
    opt_player_skeleton = "骨架", opt_player_chams = "透视",
    opt_player_tracer = "Tracer", opt_player_weapon = "武器",
    opt_ignore_teammates = "忽略队友", opt_visible_only = "仅可见",
    opt_render_distance = "渲染距离",
    opt_zombie_esp = "僵尸 ESP", opt_zombie_distance = "僵尸距离",
    opt_loot_esp = "战利品 ESP", opt_loot_distance = "战利品距离",
    opt_corpse_esp = "尸体 ESP", opt_vehicle_esp = "载具 ESP",

    opt_silent_aim = "Silent Aim", opt_rage_bot = "Rage Bot",
    opt_wall_check = "墙壁检测", opt_fov = "FOV",
    opt_smoothing = "平滑度", opt_no_recoil = "无后坐力",
    opt_no_spread = "无扩散", opt_wallbang = "Wallbang",
    opt_hitmarker = "命中标记", opt_instant_reload = "瞬间换弹",
    opt_all_fire_modes = "所有开火模式",
    opt_hbe = "命中框扩展", opt_hbe_size = "HBE 大小",
    opt_suppressed = "始终消音", opt_veh_equip = "载具内装备",

    opt_speed_hack = "加速", opt_speed_pct = "速度 %",
    opt_infinite_jump = "无限跳跃", opt_noclip = "穿墙",
    opt_noclip_speed = "穿墙速度", opt_bunny_hop = "连跳",
    opt_tp_dash = "TP Dash", opt_tp_dash_studs = "TP Dash 距离",
    opt_fly = "飞行", opt_fly_speed = "飞行速度",
    opt_car_fly = "飞行载具", opt_car_fly_speed = "飞行载具速度",
    opt_spin_bot = "Spin Bot", opt_spin_speed = "旋转速度",

    opt_inf_fuel = "无限燃料", opt_boost = "加速",
    opt_godmode = "无敌", opt_max_traction = "最大抓地力",
    opt_all_cars = "所有载具", opt_car_speed = "速度",
    opt_car_torque = "扭矩", opt_vehicle_noclip = "载具穿墙",
    opt_noclip_speed_car = "穿墙速度",
    opt_boat_mode = "船模式", opt_remove_drag = "移除阻力",
    opt_full_steer = "完全转向", opt_car_tp_dash = "载具 TP Dash",

    opt_fullbright = "全亮", opt_no_fog = "无雾",
    opt_brightness = "亮度", opt_time = "时间",
    opt_remove_shadows = "移除阴影",
    opt_remove_textures = "移除纹理", opt_remove_leaves = "移除树叶",
    opt_remove_bushes = "移除灌木",
    opt_fov_zoom = "FOV 缩放 (按住 RMB)", opt_zoom_fov = "缩放 FOV",
    opt_dist_zoom = "距离缩放", opt_normal_dist = "正常距离",
    opt_zoomed_dist = "缩放距离", opt_3p_ads = "第三人称 ADS",
    opt_remove_sunrays = "移除阳光", opt_custom_ambient = "自定义环境光",

    opt_radar_enabled = "启用", opt_radar_names = "名称",
    opt_radar_dist = "距离", opt_radar_self = "包含自己",
    opt_radar_armed = "武装颜色",
    opt_minimap = "小地图", opt_minimap_size = "小地图大小",
    opt_radar_vehicles = "雷达载具", opt_radar_corpses = "雷达尸体",

    opt_behind = "后方", opt_above = "上方", opt_side = "侧方",
    opt_tp_to_target = "传送到目标", opt_open_map = "打开地图",
    opt_respawn_tp = "重生 TP", opt_target = "目标",

    opt_freeze_zombies = "冻结僵尸", opt_whole_server = "全服务器",
    opt_freeze_anchor = "锚定", opt_radius = "范围",
    opt_anti_aim = "Anti-Aim", opt_jump_circle = "跳跃圈",
    opt_self_backtrack = "Self Backtrack", opt_instant_interact = "瞬间交互",
    opt_player_jesus = "玩家耶稣", opt_zombie_circle = "僵尸圈",
    opt_hide_player = "隐藏玩家", opt_hide_gun = "隐藏武器",
    opt_anti_fall = "Anti-Fall Stun",

    opt_enable_selection = "启用选择",
    opt_remove_all_cos = "移除所有外观",

    opt_save_config = "保存配置", opt_load_config = "加载配置",
    opt_reset_defaults = "重置默认",
    opt_minimize = "最小化 UI", opt_toggle_ui = "显示/隐藏 UI",
    opt_language = "语言",
    opt_config_name = "配置名称",
    opt_toggle_ui_key = "显示/隐藏 UI",
}

-- ============================================================
-- 4. SISTEMA DE IDIOMA
-- ============================================================
TRANSLATABLE = {}
CURRENT_LANG = "English"

function registerTranslatable(obj, key)
    if not obj or not key then return end
    table.insert(TRANSLATABLE, { obj = obj, key = key })
end

function t(key)
    local dict = LOCALES[CURRENT_LANG] or LOCALES["English"]
    local val = dict[key]
    if val ~= nil then return val end
    local en = LOCALES["English"][key]
    if en ~= nil then return en end
    return key
end

function applyLocale(lang)
    if not LOCALES[lang] then lang = "English" end
    CURRENT_LANG = lang
    local dict = LOCALES[lang]
    for _, entry in ipairs(TRANSLATABLE) do
        local translated = dict[entry.key] or LOCALES["English"][entry.key]
        if translated and entry.obj and entry.obj.Parent then
            pcall(function() entry.obj.Text = translated end)
        end
    end
end

getgenv().TetescrHUB_t = t
getgenv().TetescrHUB_applyLocale = applyLocale
getgenv().TetescrHUB_registerTranslatable = registerTranslatable
getgenv().TetescrHUB_CURRENT_LANG = function() return CURRENT_LANG end
getgenv().TetescrHUB_LOCALES = LOCALES

-- ============================================================
-- 5. PERSISTÊNCIA DO IDIOMA
-- ============================================================
CONFIG_FOLDER = "tetescrHUB_Configs"
LOCALE_FILE   = CONFIG_FOLDER .. "/locale.txt"

task.spawn(function()
    pcall(function()
        if isfolder and not isfolder(CONFIG_FOLDER) then
            if makefolder then makefolder(CONFIG_FOLDER) end
        end
    end)
    pcall(function()
        if isfile and isfile(LOCALE_FILE) then
            local saved = readfile(LOCALE_FILE)
            if saved and LOCALES[saved] then
                CURRENT_LANG = saved
            end
        end
    end)
end)

function saveLocale(lang)
    pcall(function()
        if writefile then writefile(LOCALE_FILE, lang) end
    end)
end
getgenv().TetescrHUB_saveLocale = saveLocale

-- ============================================================
-- 6. HELPERS DE UI (só o essencial pro Bloco A)
-- ============================================================
function iconFont()
    if NL.BuiltInBold then return NL.BuiltInBold end
    return Enum.Font.GothamBold
end

function clearLabelChildren(labelFrame)
    for _, c in ipairs(labelFrame:GetChildren()) do
        if c:IsA("TextLabel") then
            c.Visible = false
        elseif c:IsA("Frame") then
            local hasLayout = false
            for _, sub in ipairs(c:GetChildren()) do
                if sub:IsA("UIListLayout") then hasLayout = true break end
            end
            if not hasLayout then c.Visible = false end
        end
    end
end

getgenv().TetescrHUB_clearLabelChildren = clearLabelChildren
getgenv().TetescrHUB_iconFont = iconFont

-- ============================================================
-- FIM DO BLOCO A
-- ============================================================
print("[Tetescr hub] Bloco A carregado — idioma:", CURRENT_LANG)
print("[Tetescr hub] idiomas disponíveis:", (function()
    local list = {}
    for k, _ in pairs(LOCALES) do table.insert(list, k) end
    table.sort(list)
    return table.concat(list, ", ")
end)())


--[[
    Tetescr hub — Bloco B
    antidetect + capacidades + serviços + AR2_CFG + config manager + applyLoadedConfig
]]

-- ============================================================
-- 7. ANTIDETECT + CAPACIDADES
-- ============================================================
AR2_antidetect = { disabled = 0, hooked = false }
pcall(function()
    local ScriptError = game:GetService("ScriptContext").Error
    if getconnections then
        for _, con in ipairs(getconnections(ScriptError)) do
            if pcall(con.Disable, con) then AR2_antidetect.disabled = AR2_antidetect.disabled + 1 end
        end
    end
    if hookfunction then
        local oldCon
        oldCon = hookfunction(ScriptError.Connect, function(self, fn)
            if self == ScriptError then pcall(hookfunction, fn, function() end) end
            return oldCon(self, fn)
        end)
        AR2_antidetect.hooked = true
    end
end)

AR2_CAP = {
    getrawmetatable    = typeof(getrawmetatable) == "function",
    setreadonly         = typeof(setreadonly) == "function",
    isreadonly          = typeof(isreadonly) == "function",
    newcclosure         = typeof(newcclosure) == "function",
    hookfunction        = typeof(hookfunction) == "function",
    hookmetamethod      = typeof(hookmetamethod) == "function",
    checkcaller         = typeof(checkcaller) == "function",
    getnamecallmethod   = typeof(getnamecallmethod) == "function",
    getconnections      = typeof(getconnections) == "function",
    getgc               = typeof(getgc) == "function" or (debug and typeof(debug.getgc) == "function"),
    getupvalue          = debug and typeof(debug.getupvalue) == "function",
    setupvalue          = debug and typeof(debug.setupvalue) == "function",
    getinfo             = debug and typeof(debug.getinfo) == "function",
    getupvalues         = typeof(getupvalues) == "function" or (debug and typeof(debug.getupvalues) == "function"),
    islclosure          = typeof(islclosure) == "function",
    gethui              = typeof(gethui) == "function",
    mousemoverel        = typeof(mousemoverel) == "function" or (syn and typeof(syn.mousemoverel) == "function") or (Input and typeof(Input.mousemoverel) == "function"),
    writefile           = typeof(writefile) == "function",
    readfile            = typeof(readfile) == "function",
    isfile              = typeof(isfile) == "function",
    delfile             = typeof(delfile) == "function",
    listfiles           = typeof(listfiles) == "function",
    getcustomasset      = typeof(getcustomasset) == "function" or typeof(getsynasset) == "function",
    fireproximityprompt = typeof(fireproximityprompt) == "function",
    firetouchinterest   = typeof(firetouchinterest) == "function",
    setclipboard        = typeof(setclipboard) == "function" or typeof(toclipboard) == "function" or (syn and typeof(syn.write_clipboard) == "function") or typeof(writeclipboard) == "function",
}

AR2_hookLog = {}
local function AR2_logHook(name, ok, reason)
    AR2_hookLog[name] = { ok = ok, reason = reason or "" }
    if not ok then
        warn("[Tetescr hub] Hook skipped: " .. name .. " (" .. (reason or "unknown") .. ")")
    end
end
getgenv().AR2_logHook = AR2_logHook

do
    local missing = {}
    for k, v in pairs(AR2_CAP) do
        if not v then table.insert(missing, k) end
    end
    if #missing > 0 then
        table.sort(missing)
        warn("[Tetescr hub] Unsupported on this executor: " .. table.concat(missing, ", "))
    end
end

-- ============================================================
-- 8. SERVIÇOS + FOLDERS
-- ============================================================
AR2_Players              = game:GetService("Players")
AR2_Workspace            = game:GetService("Workspace")
AR2_CollectionService    = game:GetService("CollectionService")
AR2_HttpService          = game:GetService("HttpService")
AR2_RunService           = game:GetService("RunService")
AR2_UserInputService     = game:GetService("UserInputService")
AR2_Lighting             = game:GetService("Lighting")
AR2_ReplicatedStorage    = game:GetService("ReplicatedStorage")
AR2_ReplicatedFirst      = game:GetService("ReplicatedFirst")
AR2_LocalPlayer          = AR2_Players.LocalPlayer
AR2_Camera               = AR2_Workspace.CurrentCamera
AR2_SoundService         = game:GetService("SoundService")
AR2_TweenService         = game:GetService("TweenService")

AR2_writefile    = writefile
AR2_readfile     = readfile
AR2_isfile       = isfile
AR2_delfile      = delfile
AR2_listfiles    = listfiles
AR2_makefolder   = makefolder
AR2_isfolder     = isfolder
AR2_setclipboard = setclipboard or toclipboard or (syn and syn.write_clipboard) or writeclipboard
AR2_gethui       = gethui

function AR2_folder(name)
    return AR2_Workspace:FindFirstChild(name)
end

AR2_Characters = AR2_folder("Characters")
AR2_Corpses    = AR2_folder("Corpses")
AR2_Zombies    = AR2_folder("Zombies")
AR2_Vehicles   = AR2_folder("Vehicles")
ZombiesFolder  = AR2_Zombies

task.spawn(function()
    local wanted = {
        Characters = function(f) AR2_Characters = f ZombiesFolder = nil end,
        Corpses    = function(f) AR2_Corpses    = f end,
        Zombies    = function(f) AR2_Zombies    = f ZombiesFolder = f end,
        Vehicles   = function(f) AR2_Vehicles   = f end,
    }
    AR2_Workspace.ChildAdded:Connect(function(c)
        local set = wanted[c.Name]
        if set then set(c) end
    end)
end)

AR2_esp = {}; AR2_aim = {}; AR2_magicAim = {}; AR2_speed = {}
AR2_radar = {}; AR2_freeze = {}; AR2_visuals = {}; AR2_carMods = {}
AR2_car = {}; AR2_tp = {}

AR2_corpseMemory = {}
AR2_Players.PlayerRemoving:Connect(function(player)
    task.defer(function()
        local function snap(folder)
            if not folder then return end
            local model = folder:FindFirstChild(player.Name)
            if not model then return end
            local root = model:FindFirstChild("HumanoidRootPart") or model:FindFirstChild("Head")
            if root then
                AR2_corpseMemory[player.Name] = { name = player.Name, position = root.Position, time = os.clock() }
            end
        end
        snap(AR2_Corpses)
        if not AR2_corpseMemory[player.Name] then snap(AR2_Characters) end
    end)
end)

AR2_ORIGIN = Vector3.zero
AR2_velocityTracker = {}

-- ============================================================
-- 9. CFG — a fonte de verdade (TUDO DESLIGADO POR PADRÃO)
-- ============================================================
AR2_CFG = {
    players  = { on = false, box = false, name = false, tracer = false, weapon = false, dist = false, chams = false, maxDist = 2000, healthBar = false, skeleton = false },
    zombies  = { on = false, box = false, name = false, tracer = false, agro = false, dist = false, maxDist = 500, healthBar = false, skeleton = false },
    loot     = { on = false, box = false, name = false, tracer = false, chams = false, dist = false, maxDist = 1200, minRank = 0 },
    vehicles = { on = false, box = false, name = false, tracer = false, dist = false, maxDist = 1500, chams = false },
    corpses  = { on = false, box = false, name = false, tracer = false, dist = false, maxDist = 800, playersOnly = false },
    style    = { textSize = 13, boxThickness = 1, tracerThickness = 1, tracerFromBottom = false, cornerBox = false, fill = false, healthText = false },

    colors   = {
        player  = Color3.fromRGB(255, 80, 90),
        armed   = Color3.fromRGB(255, 140, 40),
        zombie  = Color3.fromRGB(150, 255, 120),
        agro    = Color3.fromRGB(255, 60, 60),
        vehicle = Color3.fromRGB(90, 200, 255),
        info    = Color3.fromRGB(220, 220, 225),
    },
    aim      = { on = false, key = "MouseButton2", part = "HeadCollider", fov = 180, smooth = 18, wallCheck = false, showFov = false, players = false, zombies = false, maxDist = 1500 },
    magicAim = {
    on = false, fov = 250, ignoreFov = false, maxDist = 1200, maxBend = 45,
    part = "Head", players = false, zombies = false, visCheck = false,
    showFov = false, showLockLine = false, autoSpeed = false, dropComp = false,
    bulletSpeed = 0, killSpread = false, originShift = false, redirectChance = 100,
    noTeammate = false,
},
    magicBullet = { searchRadius = 10, forwardNudge = 1 },
    rageBot = {
        on = false, maxDist = 800, ignoreFov = false, inventoryShoot = false,
        requireADS = false, forceADS = false, adsMinDist = 0, fov = 250,
        panicKey = "L", glitchWarn = false,
        visCheck = false,
    },
    weapon = {
        noRecoil = false, noSpread = false, wallbang = false, allFireModes = false,
        instantReload = false, recoilReduction = 0,
        hbeOn = false, hbeSize = 10, autoReload = false, alwaysSuppressed = false,
        vehicleEquip = false,
    },
    visuals = {
        fullbright = false, nofog = false, setTime = false, brightness = 3, time = 14,
        skybox = "Default",
        cloudsEnabled = false, modifyClouds = false,
        cloudsColor = Color3.fromRGB(255,255,255), cloudsCover = 0.5, cloudsDensity = 0.5,
        colorCorrectionEnabled = false, ccSaturation = 1, ccContrast = 0.5, ccBrightness = 1,
        removeDoors    = false,
        removeSunRays  = false,
        customAmbient  = false,
        ambientIndoor  = Color3.fromRGB(127, 127, 127),
        ambientOutdoor = Color3.fromRGB(127, 127, 127),
    },
    car = {
        fuel = false, boost = false, god = false, speed = 120, torque = 3,
        allCars = false, method = "desync",
        tpDash = false, tpDashStuds = 30, tpDashInterval = 2, tpDashVert = 0,
        fly = false, flySpeed = 100,
        boatMode = false, removeDrag = false, fullSteer = false,
        maxTraction = false,
        noclipSpeed = 90,
        noclipEjectDelay = 0.15,
        noclipTPSpeed = 800,
        noclipTPVertOffset = 5,
        noclipTPMaxSeconds = 15,
    },
    tp = {
        target = "-", behind = 10, above = 0, side = 0,
        clickKey = "T", playerKey = "Y", mapKey = "L", clickLift = 0,
        everyFrame = false, intervalCs = 9, stableCs = 100, maxSeconds = 8,
        snapBack = 8, chunked = false, hopStuds = 200, holdSecs = 0, retries = 2,
        unseat = false, guard = false, guardSecs = 10, force = false, forceSecs = 8,
        underground = false, burrowDepth = 25, burrowClip = false, stayUnder = false,
        diveKey = "G", neverGiveUp = false, waypoints = {},
        clickTPOn = false, mapTPOn = false, respawnTP = false,
        mapImageId = "102357544057594",
    },
    speed = { on = false, pct = 140, collide = false, pulse = false, pulseOn = 25, pulseOff = 35, adaptive = false, minPct = 105, airborne = false, spoof = false, spoofAs = "Falling" },
    radar = {
        on = false, names = false, localToo = false, armedColor = false, weapon = false, dist = false,
        vehicles = { on = false, names = false, dist = false },
        corpses  = { on = false, names = false, dist = false },
    },
    minimap = { on = false, size = 200, x = 10, y = 50 },
    misc = {
        infJump = false, noclip = false, noclipSpeed = 90, carNoclip = false,
        bhop = false, bhopHeight = 15,
        tpDash = false, tpDashStuds = 30, tpDashInterval = 2, tpDashVert = 0,
        fovZoom = false, zoomFOV = 30, distZoomEnabled = false, distZoomActive = false,
        normalDist = 10, zoomedDist = 3,
        thirdPersonADS = {
            enabled     = false,
            offsetRight = 2.5,
            offsetUp    = 1.2,
            offsetBack  = 3.5,
            adsFOV      = 55,
            smooth      = 0.18,
        },
        removeTextures = false, removeLeaves = false, removeBushes = false,
        hidePlayer = false, hideGun = false,
        removeReticle = false, tracers = false, tracerHue = 0,
        tracerLength = 10, tracerWidth = 2, tracerRainbow = false, tracerLightning = false, tracerStyle = "Beam",
        tracerEmission = 1, tracerGlow = 6, tracerTexLength = 4, tracerTexSpeed = 1,
        tracerLife = 0.6, tracerFade = 0.35, tracerExpand = false,
        tracerExpandSpeed = 18, tracerDamper = 0.7, tracerMax = 48,
        spinOn = false, spinSpeed = 20, spinTilt = false,
        charm = false, charmMode = "Color", charmHue = 0, charmRainbow = false,
        charmGray = 50, globalCharm = false,
        globalChams = false, globalChamsHue = 0,
        freeze = false, freezeAll = false, freezeAnchor = false, freezeRadius = 300,
        hitSound = false, hitSoundVolume = 70,
        personalCharOn = false, personalCharMode = "Color",
        personalCharHue = 0, personalCharGray = 100,
        personalCharMaterial = "ForceField", personalCharRainbow = false,
        personalGunOn = false, personalGunMode = "Color",
        personalGunHue = 0, personalGunGray = 100,
        personalGunMaterial = "Neon", personalGunRainbow = false,
        cosUnlock = false,
        instantInteract = false, playerJesus = false,
        selfBacktrack = { enabled = false, color = Color3.fromRGB(255,255,255), transparency = 0.4, material = Enum.Material.ForceField, delay = 0.3 },
        jumpCircle = { enabled = false, color = Color3.new(1,1,1), startRadius = 1, endRadius = 12, duration = 1.2 },
        removeShadows = false,
        antiAim = { enabled = false, radius = 3, speed = 5, mode = "Circle" },
        zombieCircle = { enabled = false, distance = 10, speed = 5 },
        tungSelf = false, tungGlobal = false,
        fly = false,
        flySpeedPct = 300,
        antiFallStun = false,
        morphId = "", morphPreset = "Custom", morphHideSelf = false,
        uiToggleKey = "RightShift",
    },
    ui = {
        accent       = ACCENT_DEFAULT,
        accentHue    = 210,
        background   = Color3.fromRGB(14, 14, 14),
        panel        = Color3.fromRGB(20, 20, 20),
        transparency = 0.15,
        cornerRadius = 8,
        glow         = false,
        compact      = false,
    },
    cosmetics = {
        savedOutfits      = {},
        currentOutfit     = "",
        currentOutfitName = "My Outfit",
    },
    hitmarker = {
        enabled = false, colorHue = 0, size = 20, thickness = 3,
        gap = 6, opacity = 1, shape = "X",
        sound = "None", soundPath = "", duration = 0.3, rainbow = false,
    },
    crosshair = { enabled = false, hue = 120, size = 15, thickness = 2, gap = 5, shape = "Cross", rainbow = false },
    keybinds = {
        lootESP = "None", corpseESP = "None", vehicleESP = "None",
        silentAim = "None", rageBot = "L", hbe = "None",
        noSpread = "None", instantReload = "None",
        allFireModes = "None", wallbang = "None", alwaysSuppressed = "None",
        vehicleEquip = "None", boatMode = "None", removeDrag = "None",
        fullSteer = "None", antiFallStun = "None",
        hitmarker = "None", crosshair = "None", tracers = "None",
        speedHack = "None", infJump = "None", noclip = "None", spinBot = "None",
        bhop = "None", tpDash = "None", carTpDash = "None", carFly = "None", vehicleNoclip = "None",
        fly = "F", flyRecover = "None",
        freezeZombies = "None", fullbright = "None",
        antiAim = "None", zombieCircle = "None", jumpCircle = "None",
        selfBacktrack = "None", instantInteract = "None",
        playerJesus = "None",
        thirdPersonADS = "None",
        resetConfig = "None", minimizeUI = "None",
        toggleUI = "RightShift",
    },
}

AR2_HITSOUND_PRESETS = {
    { name = "None", path = "", id = "" },
    { name = "Hitmarker Classic", path = "", id = "160432334" },
    { name = "Hitmarker Tick", path = "", id = "705502934" },
    { name = "Hitmarker Sharp", path = "", id = "8618186140" },
    { name = "Osu Hit", path = "", id = "7147454322" },
    { name = "DBZ Sword Hit", path = "", id = "232210146" },
    { name = "Flashbang", path = "", id = "2926571220" },
    { name = "Fortnite Gun", path = "", id = "3008769599" },
    { name = "Level Up", path = "", id = "2686079706" },
    { name = "Taco Bell Bong", path = "", id = "5696182212" },
    { name = "Cartoon Bonk", path = "", id = "5682262154" },
    { name = "Bruh", path = "bruh.ogg", id = "5044897021" },
    { name = "Vine Boom", path = "vineboom.ogg", id = "6308606116" },
    { name = "Vine Boom Alt", path = "", id = "5153845714" },
    { name = "Oof Hitsound", path = "oof.ogg", id = "5943191430" },
    { name = "Official Oof", path = "", id = "5987688492" },
    { name = "Sheesh", path = "", id = "6591330256" },
    { name = "Yeah Boi", path = "", id = "610314024" },
    { name = "Za Warudo", path = "", id = "5679636294" },
    { name = "Disappointed Quack", path = "", id = "767845364" },
    { name = "Pac Man Death", path = "", id = "132366334" },
    { name = "Fart", path = "", id = "6367774932" },
    { name = "Cash Register", path = "cash.ogg", id = "" },
    { name = "Pool Ball", path = "poolball.ogg", id = "" },
    { name = "Typewriter Bell", path = "typebell.ogg", id = "" },
    { name = "Bone Crack", path = "bone.ogg", id = "" },
    { name = "XP Error", path = "xperror.ogg", id = "" },
    { name = "Bubble Pop", path = "bubble.ogg", id = "" },
    { name = "Custom...", path = "", id = "" },
}

AR2_rageBotConn     = nil
AR2_rageBotHeld     = false
AR2_reloadWatchLast = 0
AR2_reloadWatchConn = nil

-- ============================================================
-- 10. STUBS — só o necessário
-- ============================================================
local function AR2_noop() end
AR2_hbeRemoveAll         = AR2_noop
AR2_setRemoveDoors       = AR2_noop

AR2_COLORS = {
    player  = Color3.fromRGB(255, 80, 90),
    armed   = Color3.fromRGB(255, 140, 40),
    zombie  = Color3.fromRGB(150, 255, 120),
    agro    = Color3.fromRGB(255, 60, 60),
    vehicle = Color3.fromRGB(90, 200, 255),
    info    = Color3.fromRGB(220, 220, 225),
}

function AR2_applyEspColors()
    local c = AR2_CFG and AR2_CFG.colors
    if type(c) ~= "table" then return end
    for k, v in pairs(c) do
        if typeof(v) == "Color3" then AR2_COLORS[k] = v end
    end
end

AR2_SETTINGS    = { menuKey = "PageDown" }
AR2_CONFIG_FILE = "tetescr_hub_config_index.json"
AR2_configList  = {}

function AR2_deepCopy(value)
    if type(value) ~= "table" then return value end
    local copy = {}
    for k, v in pairs(value) do copy[k] = AR2_deepCopy(v) end
    return copy
end
AR2_DEFAULTS = { cfg = AR2_deepCopy(AR2_CFG), settings = AR2_deepCopy(AR2_SETTINGS) }

function AR2_mergeInto(target, saved)
    if type(saved) ~= "table" then return end
    for key, current in pairs(target) do
        local incoming = saved[key]
        if type(current) == "table" then
            if next(current) == nil and type(incoming) == "table" then
                for index, value in pairs(incoming) do current[index] = value end
            else
                AR2_mergeInto(current, incoming)
            end
        elseif incoming ~= nil and type(incoming) == type(current) then
            target[key] = incoming
        end
    end
end

local function AR2_basename(path)
    return tostring(path):match("[^/\\]+$") or tostring(path)
end

function AR2_refreshConfigList()
    AR2_configList = {}
    if AR2_isfile and AR2_readfile then
        local exists = false
        pcall(function() exists = AR2_isfile(AR2_CONFIG_FILE) end)
        if exists then
            local ok, raw = pcall(AR2_readfile, AR2_CONFIG_FILE)
            if ok and raw and raw ~= "" then
                local decoded, data = pcall(AR2_HttpService.JSONDecode, AR2_HttpService, raw)
                if decoded and type(data) == "table" and type(data.configs) == "table" then
                    for _, n in ipairs(data.configs) do
                        if type(n) == "string" and n ~= "" then
                            table.insert(AR2_configList, n)
                        end
                    end
                end
            end
        end
    end
    if AR2_listfiles then
        local ok, files = pcall(AR2_listfiles)
        if ok and type(files) == "table" then
            local existing = {}
            for _, n in ipairs(AR2_configList) do existing[n] = true end
            for _, file in ipairs(files) do
                local base = AR2_basename(file)
                local name = base:match("tetescr_hub_config_(.+)%.json$")
                if name and not existing[name] then
                    table.insert(AR2_configList, name)
                    existing[name] = true
                end
            end
        end
    end
    return AR2_configList
end

function AR2_jsonSafe(v)
    local ty = typeof(v)
    if ty == "Color3" then
        return { __t = "Color3", r = v.R, g = v.G, b = v.B }
    elseif ty == "EnumItem" then
        return { __t = "Enum", e = tostring(v.EnumType), n = v.Name }
    elseif ty == "Vector3" then
        return { __t = "Vector3", x = v.X, y = v.Y, z = v.Z }
    elseif ty == "table" then
        local out = {}
        for k, vv in pairs(v) do out[k] = AR2_jsonSafe(vv) end
        return out
    end
    return v
end

function AR2_jsonRestore(v)
    if type(v) ~= "table" then return v end
    if v.__t == "Color3" then
        return Color3.new(tonumber(v.r) or 0, tonumber(v.g) or 0, tonumber(v.b) or 0)
    elseif v.__t == "Vector3" then
        return Vector3.new(tonumber(v.x) or 0, tonumber(v.y) or 0, tonumber(v.z) or 0)
    elseif v.__t == "Enum" then
        local ok, res = pcall(function()
            local et = Enum[(tostring(v.e):gsub("^Enum%.", ""))]
            return et[v.n]
        end)
        if ok and res then return res end
        return nil
    end
    local out = {}
    for k, vv in pairs(v) do out[k] = AR2_jsonRestore(vv) end
    return out
end

function AR2_saveConfig(name)
    if not AR2_writefile then
        warn("[Tetescr hub] writefile indisponível")
        return false
    end
    name = tostring(name):gsub("[^%w%s_-]", "")
    if name == "" then return false end
    local file = "tetescr_hub_config_" .. name .. ".json"
    local ok, encoded = pcall(AR2_HttpService.JSONEncode, AR2_HttpService,
        { version = 17, cfg = AR2_jsonSafe(AR2_CFG), settings = AR2_jsonSafe(AR2_SETTINGS) })
    if not ok then
        warn("[Tetescr hub] JSONEncode falhou: " .. tostring(encoded))
        return false
    end
    local fileOk, err = pcall(AR2_writefile, file, encoded)
    if not fileOk then
        warn("[Tetescr hub] writefile falhou em " .. file .. ": " .. tostring(err))
        return false
    end
    local exists = false
    for _, n in ipairs(AR2_configList) do
        if n == name then exists = true break end
    end
    if not exists then
        AR2_configList[#AR2_configList + 1] = name
    end
    local idxOk, idxEncoded = pcall(AR2_HttpService.JSONEncode, AR2_HttpService,
        { configs = AR2_configList })
    if idxOk then
        pcall(AR2_writefile, AR2_CONFIG_FILE, idxEncoded)
    end
    AR2_refreshConfigList()
    return true
end

function AR2_loadConfigByName(name)
    name = tostring(name)
    if name == "" then return false end
    local file = "tetescr_hub_config_" .. name .. ".json"
    if not (AR2_readfile and AR2_isfile) then return false end
    local present, exists = pcall(AR2_isfile, file)
    if not present or not exists then return false end
    local read, raw = pcall(AR2_readfile, file)
    if not read then return false end
    local decoded, data = pcall(AR2_HttpService.JSONDecode, AR2_HttpService, raw)
    if not decoded or type(data) ~= "table" then return false end
    AR2_mergeInto(AR2_CFG, AR2_jsonRestore(data.cfg))
    AR2_mergeInto(AR2_SETTINGS, AR2_jsonRestore(data.settings))
    if type(getgenv().AR2_applyLoadedConfig) == "function" then
        pcall(getgenv().AR2_applyLoadedConfig)
    end
    return true
end

function AR2_deleteConfig(name)
    name = tostring(name)
    if name == "" then return false end
    pcall(function()
        if AR2_isfile and AR2_isfile("tetescr_hub_config_" .. name .. ".json") then
            if AR2_delfile then AR2_delfile("tetescr_hub_config_" .. name .. ".json") end
        end
    end)
    for i, n in ipairs(AR2_configList) do
        if n == name then table.remove(AR2_configList, i) break end
    end
    local idxOk, idxEncoded = pcall(AR2_HttpService.JSONEncode, AR2_HttpService, { configs = AR2_configList })
    if idxOk then pcall(AR2_writefile, AR2_CONFIG_FILE, idxEncoded) end
    AR2_refreshConfigList()
    return true
end

function AR2_resetAllKeybinds()
    for k, _ in pairs(AR2_CFG.keybinds) do
        AR2_CFG.keybinds[k] = "None"
    end
end

-- ============================================================
-- 11. AR2_applyLoadedConfig — reativa tudo que tá on
-- ============================================================
function AR2_applyLoadedConfig()
    if AR2_esp and AR2_esp.update then pcall(AR2_esp.update) end
    if AR2_CFG.magicAim and AR2_CFG.magicAim.on then
        if AR2_magicUpdate then pcall(AR2_magicUpdate) end
    end
    if AR2_CFG.rageBot and AR2_CFG.rageBot.on then
        if AR2_rageBotSetEnabled then pcall(AR2_rageBotSetEnabled, true) end
    end
    if AR2_CFG.speed and AR2_CFG.speed.on then
        if AR2_speed and AR2_speed.start then pcall(AR2_speed.start) end
    end

    if AR2_CFG.misc and AR2_CFG.misc.fly then
        if AR2_setFly then pcall(AR2_setFly, true) end
    end
    if AR2_CFG.car and AR2_CFG.car.fly then
        if AR2_carFlySetEnabled then pcall(AR2_carFlySetEnabled, true) end
    end
    if AR2_CFG.misc and AR2_CFG.misc.noclip then
        if AR2_toggleNoclip then pcall(AR2_toggleNoclip, true) end
    end
    if AR2_CFG.misc and AR2_CFG.misc.infJump then
        if AR2_startInfJump then pcall(AR2_startInfJump) end
    end
    if AR2_CFG.misc and AR2_CFG.misc.bhop then
        if AR2_startBhop then pcall(AR2_startBhop) end
    end
    if AR2_CFG.misc and AR2_CFG.misc.tpDash then
        if AR2_startTpDash then pcall(AR2_startTpDash) end
    end
    if AR2_CFG.car and AR2_CFG.car.tpDash then
        if AR2_startCarDash then pcall(AR2_startCarDash) end
    end

    if AR2_visuals and AR2_visuals.apply then pcall(AR2_visuals.apply) end

    if AR2_CFG.misc and AR2_CFG.misc.freeze then
        if AR2_freeze and AR2_freeze.start then pcall(AR2_freeze.start) end
    end
    if AR2_CFG.radar and AR2_CFG.radar.on then
        if AR2_radar and AR2_radar.start then pcall(AR2_radar.start) end
    end
    if AR2_CFG.minimap and AR2_CFG.minimap.on then
        AR2_minimapEnabled = true
        if AR2_minimapUpdate then pcall(AR2_minimapUpdate) end
    end
    if AR2_CFG.misc and AR2_CFG.misc.tracers then
        if AR2_tracersUpdate then pcall(AR2_tracersUpdate) end
    end
    if AR2_CFG.misc and AR2_CFG.misc.spinOn then
        if AR2_setSpin then pcall(AR2_setSpin, true) end
    end
    if AR2_CFG.hitmarker and AR2_CFG.hitmarker.enabled then
        if AR2_toggleHitmarker then pcall(AR2_toggleHitmarker, true) end
    end
    if AR2_CFG.crosshair and AR2_CFG.crosshair.enabled then
        if AR2_toggleCrosshair then pcall(AR2_toggleCrosshair, true) end
    end

    if AR2_CFG.weapon then
        if AR2_CFG.weapon.wallbang and startWallbangTagging then pcall(startWallbangTagging) end
        if AR2_CFG.weapon.hbeOn and AR2_hbeSetEnabled then pcall(AR2_hbeSetEnabled, true) end
        if AR2_CFG.weapon.instantReload and setInstantReloadEnabled then pcall(setInstantReloadEnabled, true) end
        if AR2_CFG.weapon.allFireModes and AR2_applyAllFireModes then pcall(AR2_applyAllFireModes, true) end
        if AR2_CFG.weapon.alwaysSuppressed and AR2_setAlwaysSuppressed then pcall(AR2_setAlwaysSuppressed, true) end
        if AR2_CFG.weapon.vehicleEquip and AR2_setVehicleEquip then pcall(AR2_setVehicleEquip, true) end
        if AR2_CFG.weapon.autoReload and AR2_setAutoReload then pcall(AR2_setAutoReload, true) end
    end

    if AR2_CFG.car and AR2_carMods and AR2_carMods.apply then pcall(AR2_carMods.apply) end
    if AR2_CFG.car and AR2_CFG.car.boatMode and AR2_setBoatMode then pcall(AR2_setBoatMode, true) end
    if AR2_CFG.car and AR2_CFG.car.removeDrag and AR2_setRemoveDrag then pcall(AR2_setRemoveDrag, true) end
    if AR2_CFG.car and AR2_CFG.car.fullSteer and AR2_setFullSteer then pcall(AR2_setFullSteer, true) end

    if AR2_CFG.misc then
        if AR2_CFG.misc.antiAim.enabled and AR2_toggleAntiAim then pcall(AR2_toggleAntiAim, true) end
        if AR2_CFG.misc.zombieCircle.enabled and AR2_setZombieCircle then pcall(AR2_setZombieCircle, true) end
        if AR2_CFG.misc.jumpCircle.enabled and AR2_setJumpCircle then pcall(AR2_setJumpCircle, true) end
        if AR2_CFG.misc.selfBacktrack.enabled and AR2_setSelfBacktrack then pcall(AR2_setSelfBacktrack, true) end
        if AR2_CFG.misc.instantInteract and AR2_setInstantInteract then pcall(AR2_setInstantInteract, true) end
        if AR2_CFG.misc.playerJesus and AR2_setPlayerJesus then pcall(AR2_setPlayerJesus, true) end
        if AR2_CFG.misc.hidePlayer and AR2_toggleHidePlayer then pcall(AR2_toggleHidePlayer, true) end
        if AR2_CFG.misc.hideGun and AR2_toggleHideGun then pcall(AR2_toggleHideGun, true) end
        if AR2_CFG.misc.antiFallStun and AR2_setAntiFallStun then pcall(AR2_setAntiFallStun, true) end
        if AR2_CFG.misc.removeTextures and AR2_toggleRemoveTextures then pcall(AR2_toggleRemoveTextures, true) end
        if AR2_CFG.misc.removeLeaves and AR2_toggleRemoveLeaves then pcall(AR2_toggleRemoveLeaves, true) end
        if AR2_CFG.misc.removeBushes and AR2_toggleRemoveBushes then pcall(AR2_toggleRemoveBushes, true) end
        if AR2_CFG.misc.removeShadows and AR2_setRemoveShadows then pcall(AR2_setRemoveShadows, true) end
        if AR2_CFG.misc.cosUnlock and AR2_cosSetEnabled then pcall(AR2_cosSetEnabled, true) end
    end

    if AR2_CFG.visuals then
        if AR2_CFG.visuals.removeSunRays and AR2_setRemoveSunRays then pcall(AR2_setRemoveSunRays, true) end
        if AR2_CFG.visuals.customAmbient and AR2_setCustomAmbient then pcall(AR2_setCustomAmbient, true) end
        if AR2_CFG.visuals.colorCorrectionEnabled and AR2_setColorCorrection then pcall(AR2_setColorCorrection, true) end
        if AR2_CFG.visuals.cloudsEnabled and AR2_setClouds then pcall(AR2_setClouds, true) end
    end

    if AR2_CFG.misc then
        if AR2_CFG.misc.fovZoom and AR2_toggleFOVZoom then pcall(AR2_toggleFOVZoom, true) end
        if AR2_CFG.misc.distZoomEnabled and AR2_toggleDistZoomEnabled then pcall(AR2_toggleDistZoomEnabled, true) end
        if AR2_CFG.misc.thirdPersonADS.enabled and AR2_set3pAds then pcall(AR2_set3pAds, true) end
    end

if type(getgenv().AR2_refreshUI) == "function" then
    local ok, err = pcall(getgenv().AR2_refreshUI)
end
    if type(AR2_syncAllSliders) == "function" then pcall(AR2_syncAllSliders) end
end
getgenv().AR2_applyLoadedConfig = AR2_applyLoadedConfig

-- ============================================================
-- 12. RESET CFG (usado pelo botão Reset Defaults)
-- ============================================================
function AR2_resetConfig()
    print("[RESET] chamado")
    print(debug.traceback("stack:", 2))
    AR2_mergeInto(AR2_CFG, AR2_DEFAULTS.cfg)
    AR2_mergeInto(AR2_SETTINGS, AR2_DEFAULTS.settings)
    if AR2_applyLoadedConfig then pcall(AR2_applyLoadedConfig) end
end
getgenv().AR2_resetConfig = AR2_resetConfig

AR2_configLoaded = false
AR2_refreshConfigList()

getgenv().AR2_CFG = AR2_CFG

-- ============================================================
-- FIM DO BLOCO B
-- ============================================================
do
    local missing = {}
    for k, v in pairs(AR2_CAP) do
        if not v then table.insert(missing, k) end
    end
    table.sort(missing)
    local missStr = (#missing > 0) and table.concat(missing, ", ") or "nenhuma"
    print("[Tetescr hub] Bloco B carregado — AR2_CFG montado, config manager pronto")
    print("[Tetescr hub] capacidades faltando: " .. missStr)
end




--[[
    Tetescr hub — Bloco C
    ESP + aim + magic aim + rage bot + speed + freeze
]]

-- ============================================================
-- 13. HP TRACKING + DANO CLIENT-SIDE
-- ============================================================
AR2_clientHP    = {}
AR2_clientMaxHP = {}

for _, p in ipairs(AR2_Players:GetPlayers()) do
    if p ~= AR2_LocalPlayer then
        AR2_clientHP[p.Name]    = 100
        AR2_clientMaxHP[p.Name] = 100
    end
end

function AR2_getWeaponDamage(wi, hitPartName)
    if not wi then return 15 end
    local dmg = 0
    pcall(function()
        local fc = wi.FireConfig
        if not fc then return end
        dmg = fc.Damage or fc.BaseDamage or 0
        if hitPartName == "Head" or hitPartName == "HeadCollider" then
            dmg = dmg * (fc.HeadshotMultiplier or fc.HeadMultiplier or 1.5)
        end
    end)
    return dmg > 0 and dmg or 15
end

AR2_Players.PlayerRemoving:Connect(function(p)
    AR2_clientHP[p.Name]    = nil
    AR2_clientMaxHP[p.Name] = nil
end)

AR2_Players.PlayerAdded:Connect(function(p)
    AR2_clientHP[p.Name]    = 100
    AR2_clientMaxHP[p.Name] = 100
    p.CharacterAdded:Connect(function()
        task.wait(2)
        AR2_clientHP[p.Name] = 100
    end)
end)

for _, p in ipairs(AR2_Players:GetPlayers()) do
    if p ~= AR2_LocalPlayer then
        p.CharacterAdded:Connect(function()
            task.wait(2)
            AR2_clientHP[p.Name] = 100
        end)
    end
end

do
    local lastHitTarget, lastHitAt = nil, 0

    function AR2_noteDamageTarget(name)
        lastHitTarget, lastHitAt = name, os.clock()
    end

    local seen = setmetatable({}, { __mode = "k" })
    local function watch(obj)
        if seen[obj] then return end
        if not (obj:IsA("TextLabel") or obj:IsA("TextButton")) then return end
        seen[obj] = true
        local function read()
            local n = tonumber((obj.Text or ""):match("%-?%d+%.?%d*") or "")
            if not n or n <= 0 then return end
            if os.clock() - lastHitAt > 0.75 then return end
            local who = lastHitTarget
            if not who then return end
            local cur = AR2_clientHP[who] or 100
            AR2_clientHP[who] = math.max(0, cur - n)
        end
        pcall(function() obj:GetPropertyChangedSignal("Text"):Connect(read) end)
        read()
    end

    task.spawn(function()
        local PG
        while not PG do
            PG = AR2_LocalPlayer:FindFirstChildOfClass("PlayerGui")
            if not PG then task.wait(0.5) end
        end
        for _, d in ipairs(PG:GetDescendants()) do pcall(watch, d) end
        PG.DescendantAdded:Connect(function(d) pcall(watch, d) end)
    end)
end

-- ============================================================
-- 14. KEY NORMALIZE
-- ============================================================
function AR2_normalizeKey(k)
    if not k or k == "" then return "None" end
    k = tostring(k):gsub("^%s+", ""):gsub("%s+$", "")
    if k == "" then return "None" end
    local low = k:lower()
    if low == "none" then return "None" end
    local aliases = {
        ["mouse1"]="MouseButton1", ["mouse2"]="MouseButton2", ["mouse3"]="MouseButton3",
        ["m1"]="MouseButton1", ["m2"]="MouseButton2", ["m3"]="MouseButton3",
        ["ctrl"]="LeftControl", ["control"]="LeftControl",
        ["shift"]="LeftShift", ["lshift"]="LeftShift", ["rshift"]="RightShift",
        ["alt"]="LeftAlt", ["lalt"]="LeftAlt", ["ralt"]="RightAlt",
        ["esc"]="Escape", ["enter"]="Return", ["return"]="Return",
        ["spacebar"]="Space", ["space"]="Space",
        ["pageup"]="PageUp", ["pagedown"]="PageDown",
        ["caps"]="CapsLock", ["capslock"]="CapsLock",
        ["up"]="Up", ["down"]="Down", ["left"]="Left", ["right"]="Right",
        ["home"]="Home", ["end"]="End", ["insert"]="Insert", ["delete"]="Delete",
        ["del"]="Delete", ["tab"]="Tab",
    }
    if aliases[low] then return aliases[low] end
    if #k == 1 then
        local c = k:upper()
        if c >= "A" and c <= "Z" then return c end
        if tonumber(c) then return c end
    end
    local upper = k:upper()
    if upper:match("^F%d+$") then return upper end
    return k:sub(1,1):upper() .. k:sub(2)
end

-- ============================================================
-- 15. HELPERS DE ENTIDADE
-- ============================================================
function AR2_localPosition()
    local rig = AR2_LocalPlayer.Character
    local root = rig and rig:FindFirstChild("HumanoidRootPart")
    if root then return root.Position end
    return Workspace.CurrentCamera and Workspace.CurrentCamera.CFrame.Position or Vector3.zero
end

function AR2_animState(model, key, default)
    local folder = model:FindFirstChild("Animator")
    local value = folder and folder:FindFirstChild(key)
    if value and value:IsA("ValueBase") then return value.Value end
    return default
end

function AR2_parts(model)
    return {
        root = model:FindFirstChild("HumanoidRootPart"),
        head = model:FindFirstChild("Head"),
        headCollider = model:FindFirstChild("HeadCollider"),
        torso = model:FindFirstChild("UpperTorso"),
        lowerTorso = model:FindFirstChild("LowerTorso"),
    }
end

function AR2_isSquadmate(player)
    if not player or player == AR2_LocalPlayer then return false end
    local pg = AR2_LocalPlayer:FindFirstChild("PlayerGui")
    if not pg then return false end
    local sf = pg:FindFirstChild("PlayerList")
    sf = sf and sf:FindFirstChild("MainList")
    sf = sf and sf:FindFirstChild("PlayerList")
    sf = sf and sf:FindFirstChild("ScrollingFrame")
    if not sf then return false end
    for _, template in ipairs(sf:GetChildren()) do
        if template:IsA("UIListLayout") or template:IsA("UIPadding") then continue end
        local nameLabel = template:FindFirstChild("NameLabel", true)
        if not nameLabel or nameLabel.Text ~= player.Name then continue end
        local yesBox = template:FindFirstChild("YesSquadOuterBox", true)
        return yesBox and yesBox.Visible or false
    end
    return false
end

AR2_itemCache, AR2_itemCacheN = {}, 0
function AR2_equippedItem(model)
    local raw = AR2_animState(model, "EquippedItem", "")
    if raw == "" or raw == "[]" then return nil, nil end

    local hit = AR2_itemCache[raw]
    if hit then return hit.n, hit.d end

    local name, out
    local ok, data = pcall(AR2_HttpService.JSONDecode, AR2_HttpService, raw)
    if not ok or type(data) ~= "table" then
        name, out = raw, nil
    elseif data.ItemName == nil or data.ItemName == "" then
        name, out = nil, data
    else
        name, out = data.ItemName, data
    end

    if AR2_itemCacheN > 256 then AR2_itemCache, AR2_itemCacheN = {}, 0 end
    AR2_itemCache[raw] = { n = name, d = out }
    AR2_itemCacheN = AR2_itemCacheN + 1
    return name, out
end

AR2_charmData = {}
AR2_charmEnabled = false
AR2_charmMode = AR2_CFG.misc.charmMode or "Color"
AR2_charmHue = AR2_CFG.misc.charmHue or 0
AR2_charmGray = AR2_CFG.misc.charmGray or 50
AR2_charmRainbow = AR2_CFG.misc.charmRainbow or false
AR2_charmRainbowConn = nil

local function AR2_getSmoothedVelocity(model, position)
    local now = os.clock()
    local tracker = AR2_velocityTracker[model]
    local rawVelocity = Vector3.zero
    if tracker then
        local dt = now - tracker.time
        if dt > 0.01 then rawVelocity = (position - tracker.position) / dt end
    end
    local smoothed = (tracker and tracker.velocity) or Vector3.zero
    smoothed = smoothed * 0.7 + rawVelocity * 0.3
    AR2_velocityTracker[model] = { position = position, time = now, velocity = smoothed }
    return smoothed
end

function AR2_buildPlayer(model)
    local p = AR2_parts(model)
    local anchor = p.root or p.torso or p.head
    if not anchor then return nil end
    local player = AR2_Players:GetPlayerFromCharacter(model)
    local itemName = AR2_equippedItem(model)
    local velocity = AR2_getSmoothedVelocity(model, anchor.Position)
    return {
        kind = "player", model = model, player = player,
        name = player and player.Name or model.Name,
        displayName = player and player.DisplayName or model.Name,
        isLocal = player == AR2_LocalPlayer, root = anchor,
        head = p.head, headCollider = p.headCollider, torso = p.torso,
        position = anchor.Position, velocity = velocity,
        distance = (anchor.Position - AR2_ORIGIN).Magnitude,
        item = itemName, armed = itemName ~= nil,
    }
end

function AR2_buildZombie(model)
    local p = AR2_parts(model)
    local anchor = p.root or p.torso or p.head
    if not anchor then return nil end
    local velocity = AR2_getSmoothedVelocity(model, anchor.Position)
    return {
        kind = "zombie", model = model, name = model.Name,
        root = anchor, head = p.head, headCollider = p.headCollider,
        position = anchor.Position, velocity = velocity,
        distance = (anchor.Position - AR2_ORIGIN).Magnitude,
        agroState = AR2_animState(model, "AgroState", "?"),
    }
end

function AR2_buildCorpse(model)
    local p = AR2_parts(model)
    local anchor = p.root or p.torso or p.head
    if not anchor then
        for _, child in ipairs(model:GetDescendants()) do
            if child:IsA("BasePart") then anchor = child break end
        end
        if not anchor then return nil end
    end
    local player = AR2_Players:FindFirstChild(model.Name)
    return {
        kind = "corpse", model = model, name = model.Name, player = player,
        isPlayer = player ~= nil, online = player ~= nil,
        root = anchor, position = anchor.Position,
        distance = (anchor.Position - AR2_ORIGIN).Magnitude,
    }
end

function AR2_buildVehicle(model)
    local anchor = model:FindFirstChild("Base") or model.PrimaryPart
    if not anchor then return nil end
    return {
        kind = "vehicle", model = model, name = model.Name, root = anchor,
        position = anchor.Position, velocity = anchor.AssemblyLinearVelocity,
        speed = anchor.AssemblyLinearVelocity.Magnitude,
        distance = (anchor.Position - AR2_ORIGIN).Magnitude,
        moving = anchor.AssemblyLinearVelocity.Magnitude > 1,
    }
end

function AR2_collect(container, builder)
    local list = {}
    if not container then return list end
    for _, child in ipairs(container:GetChildren()) do
        if child:IsA("Model") then
            local e = builder(child)
            if e then list[#list + 1] = e end
        end
    end
    table.sort(list, function(a, b) return a.distance < b.distance end)
    return list
end

function AR2_readLootNodes(model)
    local nodes = {}
    for _, child in ipairs(model:GetChildren()) do
        if child:IsA("NumberValue") then nodes[#nodes + 1] = { name = child.Name, value = child.Value } end
    end
    return nodes
end
function AR2_collectLoot(radius, filter)
    local needle = filter and string.lower(filter) or nil
    local list = {}
    for _, model in ipairs(AR2_CollectionService:GetTagged("Loot Container")) do
        if not model:IsDescendantOf(AR2_Workspace) then continue end
        local anchor = model:FindFirstChild("BasePart") or (model:IsA("Model") and model.PrimaryPart) or (model:IsA("BasePart") and model)
        if anchor then
            local dist = (anchor.Position - AR2_ORIGIN).Magnitude
            if dist <= radius then
                local nodes = AR2_readLootNodes(model)
                local keep = needle == nil
                if needle then
                    for _, n in ipairs(nodes) do
                        if string.find(string.lower(n.name), needle, 1, true) then keep = true break end
                    end
                end
                if keep then
                    list[#list + 1] = {
                        kind = "loot", model = model, name = model.Name,
                        displayName = model:GetAttribute("DisplayName") or model.Name,
                        root = anchor, position = anchor.Position, distance = dist, nodes = nodes,
                    }
                end
            end
        end
    end
    table.sort(list, function(a, b) return a.distance < b.distance end)
    return list
end

-- ============================================================
-- 16. API DE ENTIDADES (cache)
-- ============================================================
AR2_api = {}
AR2_cache = {}

function AR2_cached(key, ttl, build)
    local entry = AR2_cache[key]
    local now = os.clock()
    if entry and (now - entry.at) < ttl then return entry.value end
    local value = build()
    AR2_cache[key] = { at = now, value = value }
    return value
end

AR2_FRESH  = 0.05
AR2_STATIC = 0.60

function AR2_api.players(includeLocal)
    local list = AR2_cached("players", AR2_FRESH, function()
        if not AR2_Characters or not AR2_Characters.Parent then
            AR2_Characters = AR2_Workspace:FindFirstChild("Characters")
        end
        AR2_ORIGIN = AR2_localPosition()
        return AR2_collect(AR2_Characters, AR2_buildPlayer)
    end)
    if includeLocal then return list end
    return AR2_cached("playersNoLocal", AR2_FRESH, function()
        local filtered = {}
        for _, e in ipairs(list) do if not e.isLocal then filtered[#filtered + 1] = e end end
        return filtered
    end)
end

function AR2_api.zombies()
    return AR2_cached("zombies", AR2_FRESH, function()
        if not AR2_Zombies or not AR2_Zombies.Parent then
            AR2_Zombies = AR2_Workspace:FindFirstChild("Zombies")
            ZombiesFolder = AR2_Zombies
        end
        AR2_ORIGIN = AR2_localPosition()
        return AR2_collect(AR2_Zombies, AR2_buildZombie)
    end)
end

function AR2_api.vehicles()
    return AR2_cached("vehicles", 0.35, function()
        if not AR2_Vehicles or not AR2_Vehicles.Parent then
            AR2_Vehicles = AR2_Workspace:FindFirstChild("Vehicles")
        end
        AR2_ORIGIN = AR2_localPosition()
        if not AR2_Vehicles then return {} end
        return AR2_collect(AR2_Vehicles, AR2_buildVehicle)
    end)
end

function AR2_api.corpses()
    return AR2_cached("corpses", 0.25, function()
        if not AR2_Corpses or not AR2_Corpses.Parent then
            AR2_Corpses = AR2_Workspace:FindFirstChild("Corpses")
        end
        if not AR2_Characters or not AR2_Characters.Parent then
            AR2_Characters = AR2_Workspace:FindFirstChild("Characters")
        end
        AR2_ORIGIN = AR2_localPosition()

        local list, seen = {}, {}
        if AR2_Corpses then
            for _, child in ipairs(AR2_Corpses:GetChildren()) do
                if child:IsA("Model") then
                    local e = AR2_buildCorpse(child)
                    if e then
                        list[#list + 1] = e
                        seen[e.name] = true
                    end
                end
            end
        end
        if AR2_Characters then
            for _, child in ipairs(AR2_Characters:GetChildren()) do
                if child:IsA("Model") and not seen[child.Name] and child ~= AR2_LocalPlayer.Character then
                    local hum = child:FindFirstChildWhichIsA("Humanoid")
                    if hum and hum.Health <= 0 then
                        local e = AR2_buildCorpse(child)
                        if e then
                            e.kind = "corpse"
                            list[#list + 1] = e
                            seen[child.Name] = true
                        end
                    end
                end
            end
        end
        local now = os.clock()
        for name, mem in pairs(AR2_corpseMemory) do
            if now - mem.time > 90 then
                AR2_corpseMemory[name] = nil
            elseif not seen[name] then
                list[#list + 1] = {
                    kind = "corpse", model = nil, name = name,
                    position = mem.position,
                    distance = (mem.position - AR2_ORIGIN).Magnitude,
                    isPlayer = true, online = false, root = nil, isGhost = true,
                }
            end
        end
        table.sort(list, function(a, b) return a.distance < b.distance end)
        return list
    end)
end

function AR2_api.loot(radius, filter)
    radius = radius or 1500
    return AR2_cached("loot:" .. radius .. ":" .. tostring(filter), 1.0, function()
        AR2_ORIGIN = AR2_localPosition()
        return AR2_collectLoot(radius, filter)
    end)
end

-- ============================================================
-- 17. RANK DE LOOT
-- ============================================================
AR2_RANK = {
    { score = 5, words = { "legendary", "exotic", "unique", "epic" } },
    { score = 4, words = { "rare", "military", "surplus", "nato", "soviet", "armory", "weapon" } },
    { score = 3, words = { "uncommon", "firearms", "gun", "attachment", "armor", "backpack" } },
    { score = 2, words = { "ammo", "medical", "medkit", "tool", "common" } },
    { score = 1, words = { "civilian", "villager", "food", "drink", "clothing", "garbage", "junk" } },
}
AR2_TIER_COLOR = {
    [5] = Color3.fromRGB(255, 200, 40), [4] = Color3.fromRGB(190, 110, 255),
    [3] = Color3.fromRGB(70, 150, 255), [2] = Color3.fromRGB(80, 220, 120),
    [1] = Color3.fromRGB(165, 165, 175), [0] = Color3.fromRGB(120, 120, 130),
}

function AR2_rankOf(entry)
    local best = 0
    local haystack = { string.lower(entry.name), string.lower(entry.displayName or "") }
    for _, node in ipairs(entry.nodes) do haystack[#haystack + 1] = string.lower(node.name) end
    for _, tier in ipairs(AR2_RANK) do
        if tier.score > best then
            for _, word in ipairs(tier.words) do
                for _, text in ipairs(haystack) do
                    if string.find(text, word, 1, true) then best = tier.score break end
                end
                if best == tier.score then break end
            end
        end
    end
    return best
end

-- ============================================================
-- 18. ESP
-- ============================================================
AR2_esp = { enabled = false, counts = {}, frameCounter = 0 }
AR2_hasDrawing = pcall(function() return Drawing.new("Line"):Remove() end)
AR2_drawPool   = {}
AR2_chamsPool  = {}
AR2_chamsFolder = nil
AR2_CHAMS_CAP  = 28

local function AR2_mkText(size)
    local t = Drawing.new("Text")
    t.Size, t.Center, t.Outline, t.Visible = size, true, true, false
    t.Color = Color3.fromRGB(235,235,235)
    pcall(function() t.OutlineColor = Color3.fromRGB(0,0,0) end)
    pcall(function() t.Font = Drawing.Fonts and Drawing.Fonts.Plex or 2 end)
    return t
end

function AR2_newVisual()
    local box = Drawing.new("Square")
    box.Thickness, box.Filled, box.Transparency, box.Visible = 1, false, 1, false
    local outline = Drawing.new("Square")
    outline.Thickness, outline.Filled, outline.Transparency, outline.Visible = 3, false, 1, false
    outline.Color = Color3.fromRGB(0,0,0)
    local fill = Drawing.new("Square")
    fill.Thickness, fill.Filled, fill.Transparency, fill.Visible = 1, true, 0.8, false
    local corners = {}
    for i = 1, 8 do
        local l = Drawing.new("Line")
        l.Thickness, l.Transparency, l.Visible = 1, 1, false
        corners[i] = l
    end
    local name = AR2_mkText(AR2_CFG.style.textSize)
    local info = AR2_mkText(AR2_CFG.style.textSize - 1)
    local htxt = AR2_mkText(AR2_CFG.style.textSize - 2)
    local tracer = Drawing.new("Line")
    tracer.Thickness, tracer.Transparency, tracer.Visible = 1, 1, false
    local hbBg = Drawing.new("Square")
    hbBg.Filled, hbBg.Color, hbBg.Transparency, hbBg.Visible = true, Color3.fromRGB(0,0,0), 0.5, false
    local hbFill = Drawing.new("Square")
    hbFill.Filled, hbFill.Color, hbFill.Transparency, hbFill.Visible = true, Color3.fromRGB(80,220,90), 1, false
    return { box=box, outline=outline, fill=fill, corners=corners,
             name=name, info=info, htxt=htxt, tracer=tracer, hbBg=hbBg, hbFill=hbFill }
end

function AR2_acquireVisual(index)
    local v = AR2_drawPool[index]
    if not v then
        for i = #AR2_drawPool + 1, index do
            AR2_drawPool[i] = AR2_newVisual()
        end
        v = AR2_drawPool[index]
    end
    return v
end

function AR2_hideVisual(v)
    v.box.Visible    = false
    if v.outline then v.outline.Visible = false end
    if v.fill    then v.fill.Visible    = false end
    if v.corners then for _, l in ipairs(v.corners) do l.Visible = false end end
    v.name.Visible   = false
    v.info.Visible   = false
    if v.htxt   then v.htxt.Visible   = false end
    v.tracer.Visible = false
    if v.hbBg   then v.hbBg.Visible   = false end
    if v.hbFill then v.hbFill.Visible = false end
end

local function AR2_paintBox(v, x, y, w, h, color)
    local th = AR2_CFG.style.boxThickness or 1
    if AR2_CFG.style.fill then
        v.fill.Position = Vector2.new(x, y)
        v.fill.Size     = Vector2.new(w, h)
        v.fill.Color    = color
        v.fill.Transparency = 0.82
        v.fill.Visible  = true
    else v.fill.Visible = false end

    if AR2_CFG.style.cornerBox == false then
        v.outline.Position = Vector2.new(x-1, y-1)
        v.outline.Size     = Vector2.new(w+2, h+2)
        v.outline.Thickness = th + 2
        v.outline.Visible  = true
        v.box.Position  = Vector2.new(x, y)
        v.box.Size      = Vector2.new(w, h)
        v.box.Color     = color
        v.box.Thickness = th
        v.box.Visible   = true
        for _, l in ipairs(v.corners) do l.Visible = false end
    else
        v.box.Visible = false
        v.outline.Visible = false
        local L = math.clamp(math.min(w, h) * 0.28, 5, 16)
        local c = v.corners
        local function seg(i, x1, y1, x2, y2)
            local l = c[i]
            l.From, l.To = Vector2.new(x1, y1), Vector2.new(x2, y2)
            l.Color, l.Thickness, l.Visible = color, th, true
        end
        seg(1, x, y,       x+L, y)        seg(2, x, y,       x, y+L)
        seg(3, x+w, y,     x+w-L, y)      seg(4, x+w, y,     x+w, y+L)
        seg(5, x, y+h,     x+L, y+h)      seg(6, x, y+h,     x, y+h-L)
        seg(7, x+w, y+h,   x+w-L, y+h)    seg(8, x+w, y+h,   x+w, y+h-L)
    end
end

local ESP_BODY_WHITELIST = {
    Head=true,
    Torso=true, HumanoidRootPart=true,
    UpperTorso=true,   LowerTorso=true,
    ["Left Arm"]=true, ["Right Arm"]=true,
    ["Left Leg"]=true, ["Right Leg"]=true,
    LeftUpperArm=true,  LeftLowerArm=true,  LeftHand=true,
    RightUpperArm=true, RightLowerArm=true, RightHand=true,
    LeftUpperLeg=true,  LeftLowerLeg=true,  LeftFoot=true,
    RightUpperLeg=true, RightLowerLeg=true, RightFoot=true,
}

function AR2_screenBox(model, useWhitelist)
    local cam = Workspace.CurrentCamera
    if not cam then return nil end
    local minX, minY = math.huge, math.huge
    local maxX, maxY = -math.huge, -math.huge
    local anyOnScreen = false

    local function addPart(part)
        if not part:IsA("BasePart") then return end
        if part:GetAttribute("ar2_hbe") then return end
        if useWhitelist and not ESP_BODY_WHITELIST[part.Name] then return end
        local cf, sz = part.CFrame, part.Size
        for x=-1,1,2 do for y=-1,1,2 do for z=-1,1,2 do
            local pt = cam:WorldToViewportPoint(
                (cf * CFrame.new(sz.X/2*x, sz.Y/2*y, sz.Z/2*z)).Position)
             if pt.Z > 0 then
                local vp = cam.ViewportSize
                if pt.X >= -200 and pt.X <= vp.X + 200
                and pt.Y >= -200 and pt.Y <= vp.Y + 200 then
                    anyOnScreen = true
                    if pt.X < minX then minX = pt.X end
                    if pt.Y < minY then minY = pt.Y end
                    if pt.X > maxX then maxX = pt.X end
                    if pt.Y > maxY then maxY = pt.Y end

                end
            end
        end end end
     end
    if model:IsA("Model") then
        for _, p in ipairs(model:GetDescendants()) do addPart(p) end
        if not anyOnScreen and useWhitelist then
            return AR2_screenBox(model, false)
        end
    elseif model:IsA("BasePart") then
        addPart(model)
    end

    if not anyOnScreen or minX == math.huge then return nil end
    return minX, minY, maxX - minX, maxY - minY
end

function AR2_readEntityHealth(model)
    local anim = model:FindFirstChild("Animator")
    if anim then
        local hp  = anim:FindFirstChild("Health")
        local mhp = anim:FindFirstChild("MaxHealth")
        if hp and hp:IsA("ValueBase") then
            local h  = tonumber(hp.Value)
            local mh = (mhp and mhp:IsA("ValueBase") and tonumber(mhp.Value)) or 100
            if h then return h, math.max(mh, 1) end
        end
    end
    local humanoid = model:FindFirstChildWhichIsA("Humanoid")
    if humanoid then
        local h  = humanoid.Health
        local mh = humanoid.MaxHealth
        if type(h) == "number" and type(mh) == "number" and mh > 0 then return h, mh end
    end
    for _, d in ipairs(model:GetDescendants()) do
        if d.Name == "Health" and d:IsA("ValueBase") then
            local h = tonumber(d.Value)
            if h then return h, 100 end
        end
    end
    return nil, nil
end

AR2_skelPool = {}
AR2_skelSlot = 0

local AR2_BONES = {
    {"Head","UpperTorso"}, {"UpperTorso","LowerTorso"},
    {"UpperTorso","LeftUpperArm"},  {"LeftUpperArm","LeftLowerArm"},  {"LeftLowerArm","LeftHand"},
    {"UpperTorso","RightUpperArm"}, {"RightUpperArm","RightLowerArm"}, {"RightLowerArm","RightHand"},
    {"LowerTorso","LeftUpperLeg"},  {"LeftUpperLeg","LeftLowerLeg"},  {"LeftLowerLeg","LeftFoot"},
    {"LowerTorso","RightUpperLeg"}, {"RightUpperLeg","RightLowerLeg"}, {"RightLowerLeg","RightFoot"},
}

local function AR2_acquireBone(i)
    local l = AR2_skelPool[i]
    if not l then
        l = Drawing.new("Line")
        l.Thickness, l.Transparency, l.Visible = 1, 1, false
        AR2_skelPool[i] = l
    end
    return l
end

function AR2_drawSkeleton(model, color)
    if not model then return end
    local cam = Workspace.CurrentCamera
    for _, bone in ipairs(AR2_BONES) do
        local a, b = model:FindFirstChild(bone[1]), model:FindFirstChild(bone[2])
        if a and b and a:IsA("BasePart") and b:IsA("BasePart") then
            local pa, oa = cam:WorldToViewportPoint(a.Position)
            local pb, ob = cam:WorldToViewportPoint(b.Position)
            if oa and ob and pa.Z > 0 and pb.Z > 0 then
                AR2_skelSlot = AR2_skelSlot + 1
                local l = AR2_acquireBone(AR2_skelSlot)
                l.From      = Vector2.new(pa.X, pa.Y)
                l.To        = Vector2.new(pb.X, pb.Y)
                l.Color     = color
                l.Thickness = AR2_CFG.style.boxThickness
                l.Visible   = true
            end
        end
    end
end

function AR2_hideSkeletons()
    for i = AR2_skelSlot + 1, #AR2_skelPool do
        AR2_skelPool[i].Visible = false
    end
end

AR2_slot = 0

local function AR2_hpColor(pct)
    pct = math.clamp(pct, 0, 1)
    if pct > 0.5 then
        local tt = (pct - 0.5) * 2
        return Color3.fromRGB(math.floor(255*(1-tt)), 210, 60)
    else
        local tt = pct * 2
        return Color3.fromRGB(230, math.floor(200*tt), 45)
    end
end

function AR2_drawEntity(model, color, label, sub, opt)
    local x, y, w, h = AR2_screenBox(model, false)
  
    if not x then return false end
    AR2_slot = AR2_slot + 1
    local v = AR2_acquireVisual(AR2_slot)
    local ts = AR2_CFG.style.textSize or 13

    if opt.box then AR2_paintBox(v, x, y, w, h, color)
    else
        v.box.Visible = false v.outline.Visible = false v.fill.Visible = false
        for _, l in ipairs(v.corners) do l.Visible = false end
    end

    if label and label ~= "" then
        v.name.Text, v.name.Color, v.name.Size = label, color, ts
        v.name.Position = Vector2.new(x + w/2, y - ts - 3)
        v.name.Visible  = true
    else v.name.Visible = false end

    if sub and sub ~= "" then
        v.info.Text, v.info.Color, v.info.Size = sub, (AR2_COLORS.info or Color3.fromRGB(220,220,225)), ts - 1
        v.info.Position = Vector2.new(x + w/2, y + h + 2)
        v.info.Visible  = true
    else v.info.Visible = false end

    if opt.tracer then
        local cam = Workspace.CurrentCamera
        local origin = AR2_CFG.style.tracerFromBottom
            and Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y)
            or  Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)
        v.tracer.From, v.tracer.To = origin, Vector2.new(x + w/2, y + h)
        v.tracer.Color, v.tracer.Thickness = color, AR2_CFG.style.tracerThickness or 1
        v.tracer.Visible = true
    else v.tracer.Visible = false end

    if opt.healthBar and v.hbBg and v.hbFill then
        local hp, maxHp
        local player = AR2_Players:GetPlayerFromCharacter(model)
        if player then hp, maxHp = AR2_clientHP[player.Name], AR2_clientMaxHP[player.Name] or 100
        else hp, maxHp = AR2_readEntityHealth(model) end
        if hp and maxHp and maxHp > 0 then
            local pct   = math.clamp(hp / maxHp, 0, 1)
            local barW  = 3
            local barX  = x - barW - 4
            local fillH = math.max(1, math.floor(h * pct))
            v.hbBg.Position = Vector2.new(barX - 1, y - 1)
            v.hbBg.Size     = Vector2.new(barW + 2, h + 2)
            v.hbBg.Color, v.hbBg.Transparency, v.hbBg.Visible = Color3.fromRGB(0,0,0), 0.45, true
            v.hbFill.Position = Vector2.new(barX, y + h - fillH)
            v.hbFill.Size     = Vector2.new(barW, fillH)
            v.hbFill.Color, v.hbFill.Transparency, v.hbFill.Visible = AR2_hpColor(pct), 0, true
            if AR2_CFG.style.healthText and v.htxt then
                v.htxt.Text = tostring(math.floor(hp))
                v.htxt.Color = AR2_hpColor(pct)
                v.htxt.Position = Vector2.new(barX - 1, y + h - fillH - ts)
                v.htxt.Center = false
                v.htxt.Visible = true
            elseif v.htxt then v.htxt.Visible = false end
        else
            v.hbBg.Visible, v.hbFill.Visible = false, false
            if v.htxt then v.htxt.Visible = false end
        end
    else
        if v.hbBg   then v.hbBg.Visible   = false end
        if v.hbFill then v.hbFill.Visible = false end
        if v.htxt   then v.htxt.Visible   = false end
    end
    return true
end

function AR2_drawPoint(pos, color, label, sub, opt)
    local cam = Workspace.CurrentCamera
    local sp, on = cam:WorldToViewportPoint(pos)
    if not on or sp.Z <= 0 then return false end
    AR2_slot = AR2_slot + 1
    local v = AR2_acquireVisual(AR2_slot)
    if v.outline then v.outline.Visible = false end
    if v.fill then v.fill.Visible = false end
    if v.corners then for _, l in ipairs(v.corners) do l.Visible = false end end
    if v.htxt then v.htxt.Visible = false end
    local s = 10
    if opt.box then
        v.box.Position  = Vector2.new(sp.X - s/2, sp.Y - s/2)
        v.box.Size      = Vector2.new(s, s)
        v.box.Color     = color
        v.box.Thickness = AR2_CFG.style.boxThickness
        v.box.Visible   = true
    else v.box.Visible = false end
    if label and label ~= "" then
        v.name.Text     = label
        v.name.Color    = color
        v.name.Size     = AR2_CFG.style.textSize
        v.name.Position = Vector2.new(sp.X, sp.Y - AR2_CFG.style.textSize - 4)
        v.name.Visible  = true
    else v.name.Visible = false end
    if sub and sub ~= "" then
        v.info.Text     = sub
        v.info.Color    = color
        v.info.Size     = AR2_CFG.style.textSize - 1
        v.info.Position = Vector2.new(sp.X, sp.Y + 6)
        v.info.Visible  = true
    else v.info.Visible = false end
    v.tracer.Visible = false
    if v.hbBg   then v.hbBg.Visible   = false end
    if v.hbFill then v.hbFill.Visible = false end
    return true
end

function AR2_ensureChamsFolder()
    if AR2_chamsFolder and AR2_chamsFolder.Parent then return end
    AR2_chamsFolder = Instance.new("Folder")
    AR2_chamsFolder.Name = "tetescr_chams"
    AR2_chamsFolder.Parent = (AR2_gethui and AR2_gethui()) or game:GetService("CoreGui")
end

AR2_chamsSlot = 0
function AR2_applyChams(model, color)
    if AR2_chamsSlot >= AR2_CHAMS_CAP then return end
    AR2_chamsSlot = AR2_chamsSlot + 1
    local h = AR2_chamsPool[AR2_chamsSlot]
    if not h then
        AR2_ensureChamsFolder()
        h = Instance.new("Highlight")
        h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        h.FillTransparency, h.OutlineTransparency = 1, 0
        h.Parent = AR2_chamsFolder
        AR2_chamsPool[AR2_chamsSlot] = h
    end
    h.Adornee, h.FillColor, h.OutlineColor, h.Enabled = model, color, Color3.new(1,1,1), true
end

function AR2_fmtDist(d) return string.format("%.0f", d) end

function AR2_buildFrame()
    AR2_slot, AR2_chamsSlot, AR2_skelSlot = 0, 0, 0
        local counts = { player = 0, zombie = 0, loot = 0, vehicle = 0, corpse = 0 }

    if AR2_CFG.players.on then
        local c = AR2_CFG.players
        for _, e in ipairs(AR2_api.players()) do
            if e.distance <= c.maxDist then
                local color = e.armed and AR2_COLORS.armed or AR2_COLORS.player
                local label = c.name and e.name or nil
                local bits = {}
                if c.weapon and e.item then bits[#bits+1] = e.item end
                if c.dist   then bits[#bits+1] = AR2_fmtDist(e.distance) .. "s" end
                if AR2_drawEntity(e.model, color, label, table.concat(bits, "  "), c) then
                    counts.player = counts.player + 1
                end
                if c.skeleton then AR2_drawSkeleton(e.model, color) end
                if AR2_CFG.misc.globalChams then
                    AR2_applyChams(e.model, Color3.fromHSV(AR2_CFG.misc.globalChamsHue / 360, 1, 1))
                elseif c.chams then
                    AR2_applyChams(e.model, color)
                end
                if AR2_CFG.misc.globalCharm and AR2_CFG.misc.charm then
                    AR2_applyCharmToCharacter(e.model)
                end
            end
        end
    end

if AR2_CFG.loot.on then
    local c = AR2_CFG.loot
    local drawn = 0
    for _, e in ipairs(AR2_api.loot(c.maxDist)) do
        if drawn >= 35 then break end
        local rank = AR2_rankOf(e)
        if rank >= c.minRank then
            local color = AR2_TIER_COLOR[rank] or AR2_TIER_COLOR[0]
            local bits = {}
            if c.dist then bits[#bits+1] = AR2_fmtDist(e.distance) .. "s" end
            if AR2_drawEntity(e.model, color, c.name and e.displayName or nil, table.concat(bits, "  "), c) then
                counts.loot = counts.loot + 1
                drawn = drawn + 1
            end
            if c.chams and rank >= 4 then AR2_applyChams(e.model, color) end
        end
    end
end

    if AR2_CFG.vehicles.on or AR2_CFG.vehicles.chams then
        local c = AR2_CFG.vehicles
        for _, e in ipairs(AR2_api.vehicles()) do
            if e.distance <= c.maxDist then
                if c.on then
                    local bits = {}
                    if e.moving then bits[#bits+1] = "MOVING" end
                    if c.dist then bits[#bits+1] = AR2_fmtDist(e.distance) .. "s" end
                    if AR2_drawEntity(e.model, AR2_COLORS.vehicle, c.name and e.name or nil, table.concat(bits, "  "), c) then
                        counts.vehicle = counts.vehicle + 1
                    end
                end
                if c.chams then AR2_applyChams(e.model, AR2_COLORS.vehicle) end
            end
        end
    end

    if AR2_CFG.corpses.on then
        local c = AR2_CFG.corpses
        local drawn = 0

 

    local corpseList = AR2_api.corpses()
    for _, e in ipairs(corpseList) do
        if drawn >= 25 then break end
        if e.distance <= (c.maxDist or 2000) then
            if not (c.playersOnly and not e.isPlayer) then
 


                    local color = e.isGhost and Color3.fromRGB(120,120,130)
                               or (e.isPlayer and Color3.fromRGB(255,170,60) or Color3.fromRGB(190,190,190))
                    local bits = {}
                    if e.isGhost then bits[#bits+1] = "last seen" end
                    if c.dist then bits[#bits+1] = AR2_fmtDist(e.distance) .. "s" end
                    if e.model then
                        if AR2_drawEntity(e.model, color, c.name and e.name or nil,
                                          table.concat(bits, "  "), c) then
                            counts.corpse = counts.corpse + 1
                            drawn = drawn + 1
                        end
                    elseif e.position then
                        if AR2_drawPoint(e.position, color, c.name and e.name or nil,
                                         table.concat(bits, "  "), c) then
                            counts.corpse = counts.corpse + 1
                            drawn = drawn + 1
                        end
                    end
                end
            end
        end
    end

    for i = AR2_slot + 1, #AR2_drawPool do AR2_hideVisual(AR2_drawPool[i]) end
    AR2_hideSkeletons()
    for i = AR2_chamsSlot + 1, #AR2_chamsPool do
        AR2_chamsPool[i].Adornee = nil
        AR2_chamsPool[i].Enabled = false
    end
    AR2_esp.counts = counts
end

AR2_espConn = nil
function AR2_esp.update()
    local anyOn = AR2_CFG.players.on or AR2_CFG.loot.on or AR2_CFG.vehicles.on or AR2_CFG.corpses.on
    if anyOn and not AR2_espConn then
        AR2_esp.start()
    elseif not anyOn and AR2_espConn then
        AR2_esp.stop()
    end
end

function AR2_esp.start()
    if AR2_espConn then return end
    if not AR2_hasDrawing then return end
    AR2_esp.enabled = true
    AR2_espConn = AR2_RunService.RenderStepped:Connect(function()
        AR2_Camera = Workspace.CurrentCamera
        local ok, err = pcall(AR2_buildFrame)
        if not ok then
            warn("[esp error] " .. tostring(err))
        end
    end)
end

function AR2_esp.stop()
    AR2_esp.enabled = false
    if AR2_espConn then AR2_espConn:Disconnect() AR2_espConn = nil end
    for _, v in ipairs(AR2_drawPool) do AR2_hideVisual(v) end
    for _, h in ipairs(AR2_chamsPool) do h.Adornee = nil h.Enabled = false end
    AR2_hideSkeletons()
end

function AR2_esp.destroy()
    AR2_esp.stop()
    for _, v in ipairs(AR2_drawPool) do
        v.box:Remove()
        v.name:Remove()
        v.info:Remove()
        v.tracer:Remove()
        if v.hbBg   then v.hbBg:Remove()   end
        if v.hbFill then v.hbFill:Remove() end
    end
    AR2_drawPool = {}
    for _, l in ipairs(AR2_skelPool) do l:Remove() end
    AR2_skelPool = {}
    for _, h in ipairs(AR2_chamsPool) do h:Destroy() end
    AR2_chamsPool = {}
    if AR2_chamsFolder then AR2_chamsFolder:Destroy() AR2_chamsFolder = nil end
end

-- ============================================================
-- 19. AIMBOT
-- ============================================================
AR2_aim = { aiming = false, target = nil, hasMouseMove = false }
AR2_aimConn = nil
AR2_fovCircle = nil
AR2_mousemoverel = mousemoverel or (syn and syn.mousemoverel) or (Input and Input.mousemoverel)
AR2_aim.hasMouseMove = AR2_mousemoverel ~= nil
AR2_AIM_PARTS = { "HeadCollider", "Head", "UpperTorso", "Root" }
AR2_MAGIC_AIM_PARTS = { "Head", "UpperTorso", "Root" }

function AR2_heldKey(name)
    if name == "MouseButton1" then return AR2_UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) end
    if name == "MouseButton2" then return AR2_UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) end
    if name == "MouseButton3" then return AR2_UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton3) end
    local code = Enum.KeyCode[name]
    return code ~= nil and AR2_UserInputService:IsKeyDown(code)
end

function AR2_aimPartOf(entity, preference)
    if preference == "HeadCollider" then return entity.headCollider or entity.head or entity.torso or entity.root
    elseif preference == "Head" then return entity.head or entity.torso or entity.root
    elseif preference == "UpperTorso" then return entity.torso or entity.root end
    return entity.root
end

function AR2_isVisible(model, part)
    if not part then return false end
    local cam = Workspace.CurrentCamera
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = { AR2_LocalPlayer.Character, model }
    local origin = cam.CFrame.Position
    return AR2_Workspace:Raycast(origin, part.Position - origin, params) == nil
end

function AR2_getTarget(mousePos)
    local cam = Workspace.CurrentCamera
    local c = AR2_CFG.aim
    local best, bestPart, bestDist = nil, nil, c.fov
    local pool = {}
    if c.players then for _, e in ipairs(AR2_api.players()) do pool[#pool+1] = e end end
    if c.zombies then for _, e in ipairs(AR2_api.zombies()) do pool[#pool+1] = e end end
    for _, entity in ipairs(pool) do
        if entity.distance <= c.maxDist then
            local part = AR2_aimPartOf(entity, c.part)
            if part then
                local screenPos, onScreen = cam:WorldToViewportPoint(part.Position)
                if onScreen and screenPos.Z > 0 then
                    local flat = (mousePos - Vector2.new(screenPos.X, screenPos.Y)).Magnitude
                    if flat < bestDist then
                        if not c.wallCheck or AR2_isVisible(entity.model, part) then
                            best, bestPart, bestDist = entity, part, flat
                        end
                    end
                end
            end
        end
    end
    return best, bestPart
end

function AR2_stepAim()
    local cam = Workspace.CurrentCamera
    local c = AR2_CFG.aim
    local mousePos = AR2_UserInputService:GetMouseLocation()
    AR2_aim.aiming = c.on and AR2_heldKey(c.key)
    if AR2_fovCircle then
        AR2_fovCircle.Visible = c.on and c.showFov
        if AR2_fovCircle.Visible then
            AR2_fovCircle.Radius = c.fov
            AR2_fovCircle.Position = mousePos
            AR2_fovCircle.Color = AR2_aim.aiming and Color3.fromRGB(255,80,90) or Color3.fromRGB(255,200,40)
        end
    end
    if not AR2_aim.aiming then AR2_aim.target = nil return end
    local entity, part = AR2_getTarget(mousePos)
    AR2_aim.target = entity
    if not part then return end
    local screenPos = cam:WorldToViewportPoint(part.Position)
    local delta = Vector2.new(screenPos.X, screenPos.Y) - mousePos
    local smooth = math.clamp(c.smooth / 100, 0.01, 1)
    if AR2_mousemoverel and AR2_CAP.mousemoverel then
        pcall(AR2_mousemoverel, delta.X * smooth, delta.Y * smooth)
    else
        local desired = CFrame.new(cam.CFrame.Position, part.Position)
        cam.CFrame = cam.CFrame:Lerp(desired, smooth)
    end
end

function AR2_aim.start()
    if AR2_aimConn then return end
    if AR2_hasDrawing and not AR2_fovCircle then
        AR2_fovCircle = Drawing.new("Circle")
        AR2_fovCircle.Thickness, AR2_fovCircle.Filled, AR2_fovCircle.Transparency = 1, false, 1
        AR2_fovCircle.NumSides, AR2_fovCircle.Visible = 64, false
    end
    AR2_aimConn = AR2_RunService.RenderStepped:Connect(function()
        local ok, err = pcall(AR2_stepAim)
        if not ok then warn("[aim] " .. tostring(err)) end
    end)
end

function AR2_aim.stop()
    if AR2_aimConn then AR2_aimConn:Disconnect() AR2_aimConn = nil end
    AR2_aim.aiming, AR2_aim.target = false, nil
    if AR2_fovCircle then AR2_fovCircle.Visible = false end
end

function AR2_aim.destroy()
    AR2_aim.stop()
    if AR2_fovCircle then AR2_fovCircle:Remove() AR2_fovCircle = nil end
end

-- ============================================================
-- 20. MAGIC AIM
-- ============================================================
AR2_getgc = getgc or (debug and debug.getgc)
AR2_getinfo = (debug and debug.getinfo) or getinfo
AR2_getupvalues = (debug and debug.getupvalues) or getupvalues
AR2_islclosure = islclosure
AR2_hookfunction = hookfunction or (syn and syn.hookfunction) or replaceclosure

AR2_magicAim = { supported = (AR2_CAP.getgc == true), shots = 0, redirected = 0, lastBend = 0, target = nil, lastSpeed = nil, widestRefused = 0 }
AR2_magicCircle = nil
AR2_magicConn = nil
AR2_magicLockLine = nil

function AR2_getEquippedItemName()
    local ok, pc = pcall(function()
        return require(AR2_ReplicatedFirst:WaitForChild("Framework", 30)).Classes.Players.get()
    end)
    if ok and pc and pc.Character then
        local item = pc.Character.EquippedItem
        if item then
            local inner = rawget(item, "__item") or item
            if inner.ItemName then return inner.ItemName end
            if inner.Name then return inner.Name end
            if inner.WeaponName then return inner.WeaponName end
            for _, child in ipairs(item:GetChildren()) do
                if child:IsA("Tool") then return child.Name end
            end
        end
    end
    local char = AR2_LocalPlayer.Character
    if char then
        local animator = char:FindFirstChild("Animator")
        if animator then
            local eq = animator:FindFirstChild("EquippedItem")
            if eq and eq:IsA("ValueBase") then
                local raw = eq.Value
                if type(raw) == "string" and raw ~= "" then return raw end
                if type(raw) == "table" then return raw.ItemName or raw.Name end
            end
        end
    end
    local backpack = AR2_LocalPlayer:FindFirstChild("Backpack")
    if backpack then
        for _, tool in ipairs(backpack:GetChildren()) do
            if tool:IsA("Tool") then return tool.Name end
        end
    end
    return nil
end

function AR2_getItemDataModule()
    local replicated = AR2_ReplicatedStorage
    local client = replicated:FindFirstChild("Client")
    if client then
        local configs = client:FindFirstChild("Configs")
        if configs then
            local itemData = configs:FindFirstChild("ItemData")
            if itemData then
                local ok, ItemData = pcall(require, itemData)
                if ok and type(ItemData) == "table" then return ItemData end
            end
        end
    end
    for _, obj in ipairs(replicated:GetDescendants()) do
        if obj:IsA("ModuleScript") and obj.Name == "ItemData" then
            local ok, ItemData = pcall(require, obj)
            if ok and type(ItemData) == "table" then return ItemData end
        end
    end
    return nil
end

function AR2_getBulletSpeed()
    local c = AR2_CFG.magicAim
    local base
    if not c.autoSpeed and c.bulletSpeed and c.bulletSpeed > 0 then
        base = c.bulletSpeed
    else
        local weaponName = AR2_getEquippedItemName()
        if weaponName then
            local ItemData = AR2_getItemDataModule()
            if ItemData and ItemData[weaponName] and ItemData[weaponName].FireConfig then
                local speed = ItemData[weaponName].FireConfig.MuzzleVelocity
                if type(speed) == "number" and speed > 100 then base = speed end
            end
        end
        base = base or 4700
    end
    return base
end

AR2_gravSeen = {}
local function AR2_reportBallistics(name, fcGrav, fcDrop, muzzle, resolved)
    if not name or AR2_gravSeen[name] then return end
    AR2_gravSeen[name] = true
    print(string.format(
        "[AR2 BALLISTICS] %s | fc.Gravity=%s fc.BulletDrop=%s MuzzleVelocity=%s | using g=%.1f (world=%.1f)",
        tostring(name), tostring(fcGrav), tostring(fcDrop), tostring(muzzle),
        resolved, AR2_Workspace.Gravity))
end

function AR2_getBulletGravity()
    local g = nil
    local fcGrav, fcDrop, muzzle = nil, nil, nil
    local weaponName = AR2_getEquippedItemName()
    if weaponName then
        local ItemData = AR2_getItemDataModule()
        local fc = ItemData and ItemData[weaponName] and ItemData[weaponName].FireConfig
        if fc then
            fcGrav, fcDrop, muzzle = fc.Gravity, fc.BulletDrop, fc.MuzzleVelocity
            local v = fc.Gravity
            if type(v) ~= "number" then v = fc.BulletDrop end
            if type(v) == "number" and v > 0 then
                g = (v < 20) and (AR2_Workspace.Gravity * v) or v
            elseif type(v) == "number" and v == 0 then
                g = 0
            end
        end
    end
    if g == nil then g = AR2_Workspace.Gravity * 0.6 end
    AR2_reportBallistics(weaponName, fcGrav, fcDrop, muzzle, g)
    return g
end

function AR2_predict(part, origin, velocity)
    if not part then return origin end
    local speed = AR2_getBulletSpeed()
    if not speed or speed <= 0 then return part.Position end
    velocity = velocity or Vector3.zero
    local c = AR2_CFG.magicAim

    local g = 0
    if c.dropComp then g = AR2_getBulletGravity() end

    local vy = velocity.Y
    if math.abs(vy) < 12 then vy = 0 end

    local hx, hz = velocity.X, velocity.Z
    if math.sqrt(hx * hx + hz * hz) < 2 then hx, hz = 0, 0 end

    local vLead = Vector3.new(hx, vy, hz)
    local base   = part.Position
    local point  = base
    local flight = (point - origin).Magnitude / speed
    for _ = 1, 8 do
        point = base + vLead * flight
        if g > 0 then
            point = point + Vector3.new(0, 0.5 * g * flight * flight, 0)
        end
        local newFlight = (point - origin).Magnitude / speed
        if math.abs(newFlight - flight) < 0.001 then break end
        flight = newFlight
    end
    return point
end

function AR2_findBestOrigin(origin, direction, targetPos, targetChar)
    local rayParams = RaycastParams.new()
    rayParams.FilterType = Enum.RaycastFilterType.Exclude
    rayParams.FilterDescendantsInstances = { AR2_LocalPlayer.Character, targetChar }
    local dirs = {
        Vector3.new(1,0,0), Vector3.new(-1,0,0), Vector3.new(0,1,0), Vector3.new(0,-1,0),
        Vector3.new(0,0,1), Vector3.new(0,0,-1)
    }
    for x=-1,1 do for y=-1,1 do for z=-1,1 do
        if math.abs(x)+math.abs(y)+math.abs(z) > 1 then table.insert(dirs, Vector3.new(x,y,z).Unit) end
    end end end
    table.insert(dirs, direction.Unit)
    local bestOrigin = origin
    local bestScore = -math.huge
    local foundClear = false
    local maxRadius = math.min(AR2_CFG.magicBullet.searchRadius, 12)
    local stepSize = math.max(0.3, maxRadius/30)
    for _, dir in ipairs(dirs) do
        for step = stepSize, maxRadius, stepSize do
            local newOrigin = origin + dir*step
            local result = AR2_Workspace:Raycast(newOrigin, (targetPos-newOrigin).Unit * (targetPos-newOrigin).Magnitude, rayParams)
            if not result then
                if not foundClear then bestOrigin = newOrigin foundClear = true end
            else
                if not foundClear then
                    local dist = (newOrigin - result.Position).Magnitude
                    if dist > bestScore then bestScore = dist bestOrigin = newOrigin end
                end
            end
        end
    end
    if foundClear then return bestOrigin, true end
    return bestOrigin, false
end

function AR2_magicTarget()
    local cam = Workspace.CurrentCamera
    local c = AR2_CFG.magicAim
    local centre = Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)
    local best, bestPart, bestScore = nil, nil, (c.ignoreFov and math.huge or c.fov)
    local pool = {}
    if c.players then
        for _, e in ipairs(AR2_api.players()) do
            local isTeammate = false
            if c.noTeammate and e.player then
                if AR2_isSquadmate(e.player) then isTeammate = true end
            end
            if not isTeammate then pool[#pool+1] = e end
        end
    end
    if c.zombies then for _, e in ipairs(AR2_api.zombies()) do pool[#pool+1] = e end end
    for _, entity in ipairs(pool) do
        if entity.distance <= c.maxDist then
            local part = AR2_aimPartOf(entity, c.part)
            if part then
                local screenPos, onScreen = cam:WorldToViewportPoint(part.Position)
                if c.ignoreFov or (onScreen and screenPos.Z > 0) then
                    local score = c.ignoreFov and entity.distance or (centre - Vector2.new(screenPos.X, screenPos.Y)).Magnitude
                    if score < bestScore then
                        if not c.visCheck or AR2_isVisible(entity.model, part) then
                            best, bestPart, bestScore = entity, part, score
                        end
                    end
                end
            end
        end
    end
    return best, bestPart
end

function AR2_magicUpdate()
    local shouldRun = AR2_CFG.magicAim.on and (AR2_CFG.magicAim.showFov or AR2_CFG.magicAim.showLockLine)
    if shouldRun and not AR2_magicConn then
        if not AR2_hasDrawing then return end
        AR2_magicCircle = Drawing.new("Circle")
        AR2_magicCircle.Thickness, AR2_magicCircle.Filled, AR2_magicCircle.Transparency = 1, false, 1
        AR2_magicCircle.NumSides, AR2_magicCircle.Visible = 64, true
        AR2_magicLockLine = Drawing.new("Line")
        AR2_magicLockLine.Thickness, AR2_magicLockLine.Transparency, AR2_magicLockLine.Visible = 1, 1, false
        AR2_magicConn = AR2_RunService.RenderStepped:Connect(function()
            local cam = Workspace.CurrentCamera
            local centre = Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)
            local entity, part = AR2_magicTarget()
            AR2_magicAim.target = entity
            if AR2_CFG.magicAim.showFov then
                AR2_magicCircle.Position = centre
                AR2_magicCircle.Radius = AR2_CFG.magicAim.fov
                AR2_magicCircle.Color = AR2_magicAim.target and Color3.fromRGB(255,80,90) or Color3.fromRGB(150,90,200)
                AR2_magicCircle.Visible = true
            else AR2_magicCircle.Visible = false end
            if AR2_CFG.magicAim.showLockLine then
                if part then
                    local screenPos, onScreen = cam:WorldToViewportPoint(part.Position)
                    if onScreen and screenPos.Z > 0 then
                        AR2_magicLockLine.From = centre
                        AR2_magicLockLine.To = Vector2.new(screenPos.X, screenPos.Y)
                        AR2_magicLockLine.Color = Color3.fromRGB(255,80,90)
                        AR2_magicLockLine.Visible = true
                    else AR2_magicLockLine.Visible = false end
                else AR2_magicLockLine.Visible = false end
            else AR2_magicLockLine.Visible = false end
        end)
    elseif not shouldRun and AR2_magicConn then
        AR2_magicConn:Disconnect()
        AR2_magicConn = nil
        if AR2_magicCircle then AR2_magicCircle.Visible = false end
        if AR2_magicLockLine then AR2_magicLockLine.Visible = false end
    end
end

function AR2_toggleWorldwide(enabled)
    AR2_CFG.magicAim.ignoreFov = enabled
    AR2_magicUpdate()
end

-- ============================================================
-- 21. HOOK DE FIRE
-- ============================================================
AR2_success, AR2_Bullets = pcall(function()
    return require(AR2_ReplicatedFirst:WaitForChild("Framework", 30)).Libraries.Bullets
end)
AR2_originalFireFunction = nil
AR2_recoilOriginalFn     = nil
AR2_spreadOriginalFn     = nil

if AR2_success and AR2_Bullets and AR2_Bullets.Fire then
    AR2_originalFireFunction = AR2_Bullets.Fire

    if AR2_CAP.getupvalue then
        pcall(function() AR2_recoilOriginalFn = debug.getupvalue(AR2_originalFireFunction, 6) end)
        pcall(function() AR2_spreadOriginalFn = debug.getupvalue(AR2_originalFireFunction, 3) end)
    end

    local function AR2_identitySpread(rng, direction)
        return direction.Unit
    end

    AR2_Bullets.Fire = function(self, playerController, weaponInstance, weaponData, origin, direction, shotId)
        AR2_lastFireParams = { self = self, pc = playerController, wi = weaponInstance, wd = weaponData }
        AR2_hasRealWD      = true
        AR2_autoSeeded     = true
        AR2_autoSeeding    = false
        if AR2_tryCacheAmmoRef then pcall(AR2_tryCacheAmmoRef, weaponInstance) end

        pcall(function() AR2_gameChar = playerController and playerController.Character or nil end)

        local ok, result = pcall(function()
            local originalOrigin    = origin
            local originalDirection = direction

                    local aimEntity, aimPart = nil, nil
        if AR2_CFG.magicAim.on or AR2_CFG.rageBot.on then
            aimEntity, aimPart = AR2_magicTarget()
            AR2_magicAim.target = aimEntity
        end

        if AR2_CFG.magicAim.on then
            print(string.format("[FIRE HOOK] chamou! magicAim.on=%s | target=%s | part=%s | dir_antes=%s",
                tostring(AR2_CFG.magicAim.on),
                tostring(aimEntity and aimEntity.name),
                tostring(aimPart and aimPart.Name),
                tostring(direction)))
        end

            if (AR2_CFG.magicAim.on or AR2_CFG.rageBot.on) and aimEntity and aimPart then
                if AR2_CFG.magicAim.originShift then
                    if aimEntity.model then
                        local headPart = aimEntity.headCollider or aimEntity.head
                        if headPart then
                            local nudge = originalOrigin + originalDirection * AR2_CFG.magicBullet.forwardNudge
                            local newOrigin, clear = AR2_findBestOrigin(nudge, originalDirection, headPart.Position, aimEntity.model)
                            if clear or newOrigin ~= originalOrigin then
                                origin = newOrigin
                                direction = (headPart.Position - origin).Unit
                            end
                        end
                    end
                end
                local aimPoint = AR2_predict(aimPart, origin, aimEntity.velocity)
                local bend = aimPoint - origin
                if bend.Magnitude > 0.001 then
                    local angle = math.deg(math.acos(math.clamp(bend.Unit:Dot(direction.Unit), -1, 1)))
                    local chance = AR2_CFG.magicAim.redirectChance or 100
                    if angle <= AR2_CFG.magicAim.maxBend and math.random(1,100) <= chance then
                        direction = bend.Unit
                        if aimEntity.name and AR2_noteDamageTarget then AR2_noteDamageTarget(aimEntity.name) end
                    end
                end
            end

            local applyNoSpread = AR2_CFG.weapon.noSpread
            if AR2_CFG.magicAim.killSpread and aimEntity ~= nil then applyNoSpread = true end

            if applyNoSpread and AR2_CAP.setupvalue and AR2_spreadOriginalFn then
                pcall(function() debug.setupvalue(AR2_originalFireFunction, 3, AR2_identitySpread) end)
            else
                if AR2_spreadOriginalFn then
                    pcall(debug.setupvalue, AR2_originalFireFunction, 3, AR2_spreadOriginalFn)
                end
            end

            if AR2_CFG.weapon.noRecoil and AR2_CAP.setupvalue then
                pcall(function()
                    debug.setupvalue(AR2_originalFireFunction, 6,
                        function(...) return Vector2.new(0,0), 0, 0, 0, 0 end)
                end)
            elseif AR2_CFG.weapon.recoilReduction and AR2_CFG.weapon.recoilReduction > 0 and AR2_CAP.setupvalue then
                local reduction = AR2_CFG.weapon.recoilReduction / 100
                if AR2_recoilOriginalFn then
                    local function newRecoil(...)
                        local x, y, z, a, b = AR2_recoilOriginalFn(...)
                        return Vector2.new(x.X*(1-reduction), x.Y*(1-reduction)),
                               z*(1-reduction), a*(1-reduction), b*(1-reduction), 0
                    end
                    pcall(debug.setupvalue, AR2_originalFireFunction, 6, newRecoil)
                end
            else
                if AR2_recoilOriginalFn and AR2_CAP.setupvalue then
                    pcall(debug.setupvalue, AR2_originalFireFunction, 6, AR2_recoilOriginalFn)
                end
            end

            if AR2_CFG.weapon.wallbang then
                local penFields = {
                    "Penetration","PenetrationPower","WallPenetration",
                    "WallPenetrationPower","PierceCount","Piercing",
                    "BulletPenetration","ArmorPenetration","PenPower",
                }
                for _, f in ipairs(penFields) do
                    pcall(function() weaponData[f] = 9999 end)
                end
                pcall(function() weaponData.CanPenetrate    = true end)
                pcall(function() weaponData.PenetrateWalls  = true end)
                pcall(function() weaponData.IgnoreArmor     = true end)
                pcall(function()
                    if weaponData.FireConfig then
                        weaponData.FireConfig.Penetration = 9999
                        weaponData.FireConfig.PenetrationPower = 9999
                    end
                end)
            end

            local result = AR2_originalFireFunction(self, playerController, weaponInstance, weaponData, origin, direction, shotId)

            if AR2_tracersEnabled and AR2_addTracer then AR2_addTracer(origin, direction) end

            if AR2_CFG.hitmarker.enabled then
                local _rp=RaycastParams.new()
                _rp.FilterType=Enum.RaycastFilterType.Exclude
                _rp.FilterDescendantsInstances={AR2_LocalPlayer.Character}
                local _hit=AR2_Workspace:Raycast(origin,direction*2000,_rp)
                local _det=false
                if _hit and _hit.Instance then
                    local _m=_hit.Instance
                    while _m and not _m:IsA("Model") do _m=_m.Parent end
                    if _m then
                        _det=(AR2_Characters~=nil and _m.Parent==AR2_Characters)
                            or(ZombiesFolder~=nil and _m.Parent==ZombiesFolder)
                            or _m:FindFirstChildOfClass("Humanoid")~=nil
                    end
                end
                if _det then AR2_hitmarkerFlag = true end
            end

            return result
        end)
        if not ok then
            warn("[fire] hook error: " .. tostring(result))
            return AR2_originalFireFunction(self, playerController, weaponInstance, weaponData, origin, direction, shotId)
        end
        return result
    end
else
    warn("[Tetescr hub] Framework.Libraries.Bullets indisponível — magic aim / no recoil / no spread / wallbang vão ficar inativos")
end

-- ============================================================
-- 22. RAGE BOT
-- ============================================================
AR2_rbLastFire = 0
AR2_lastFireParams = nil
AR2_rbHeld = false
AR2_adsHeld = false
AR2_autoSeeding = false
AR2_autoSeeded = false
AR2_hasRealWD = false
AR2_lastTargetDist = 0
AR2_cachedAmmoItem = nil
local AR2_VIM = game:GetService("VirtualInputManager")

AR2_gameChar = nil

local function AR2_tryCacheAmmoRef(inner)
    if not inner or type(inner) ~= "table" then return end
    pcall(function()
        local att      = rawget(inner, "Attachments") or inner.Attachments
        if not att then return end
        local ammoSlot = rawget(att, "Ammo") or att.Ammo
        if not ammoSlot then return end
        local ammoItem = rawget(ammoSlot, "__item") or ammoSlot
        if ammoItem and ammoItem.WorkingAmount ~= nil then
            AR2_cachedAmmoItem = ammoItem
        end
    end)
end
AR2_tryCacheAmmoRef = AR2_tryCacheAmmoRef

local function AR2_readAmmoFromInner(inner)
    if not inner or type(inner) ~= "table" then return nil end
    local result = nil
    pcall(function()
        local att = rawget(inner, "Attachments") or inner.Attachments
        if att then
            local ammoSlot = rawget(att, "Ammo") or att.Ammo
            if ammoSlot then
                local ammoItem = rawget(ammoSlot, "__item") or ammoSlot
                if ammoItem then
                    if ammoItem.WorkingAmount ~= nil then result = ammoItem.WorkingAmount; return end
                    if ammoItem.Amount        ~= nil then result = ammoItem.Amount; return end
                end
            end
        end
    end)
    if result ~= nil then return result end
    pcall(function() if inner.WorkingAmount ~= nil then result = inner.WorkingAmount end end)
    if result ~= nil then return result end
    pcall(function() if inner.Magazine      ~= nil then result = inner.Magazine end end)
    return result
end

local function AR2_getAmmoLive()
    local pc = AR2_getPlayerClass and AR2_getPlayerClass()
    if pc and pc.Character then
        local item = pc.Character.EquippedItem
        if item then
            local inner = rawget(item, "__item") or item
            local ammo  = AR2_readAmmoFromInner(inner)
            if ammo ~= nil then return ammo end
        end
    end
    if AR2_lastFireParams and AR2_lastFireParams.wi then
        return AR2_readAmmoFromInner(AR2_lastFireParams.wi)
    end
    return nil
end

local function AR2_getReloading()
    local inner = AR2_getEquippedInner and AR2_getEquippedInner()
    if not inner and AR2_lastFireParams then inner = AR2_lastFireParams.wi end
    if not inner then return false end
    local r = false
    pcall(function() r = inner.Reloading == true end)
    return r
end

local function AR2_getFireInterval(wi)
    if wi and wi.FireConfig and wi.FireConfig.FireRate then
        return math.max(0.05, 60 / wi.FireConfig.FireRate)
    end
    return 0.1
end

local function AR2_releaseVIM()
    if AR2_rbHeld then
        pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,0,false,game,1) end)
        AR2_rbHeld = false
    end
end

local function AR2_releaseADS()
    if AR2_adsHeld then
        pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,1,false,game,1) end)
        AR2_adsHeld = false
    end
end

function AR2_rageBotVisCheck(model, part, origin)
    if not part then return false end
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = { AR2_LocalPlayer.Character, model }
    params.IgnoreWater = true
    local hit = AR2_Workspace:Raycast(origin, part.Position - origin, params)
    return hit == nil
end

local function AR2_rageBotTarget()
    local cam    = workspace.CurrentCamera
    local rc     = AR2_CFG.rageBot
    local centre = Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)
    local best, bestPart, bestScore, bestDist = nil, nil, math.huge, 0
    local origin = AR2_localPosition()

    local Characters = workspace:FindFirstChild("Characters")
    if not Characters then return nil, nil, 0 end

    for _, model in ipairs(Characters:GetChildren()) do
        if model ~= AR2_LocalPlayer.Character and model:IsA("Model") then
            local player = AR2_Players:GetPlayerFromCharacter(model)
            if AR2_CFG.magicAim.noTeammate and player then
                if AR2_isSquadmate(player) then continue end
            end

            local part = model:FindFirstChild("HeadCollider")
                      or model:FindFirstChild("Head")
                      or model:FindFirstChild("HumanoidRootPart")
            if part then
                local dist = (part.Position - origin).Magnitude
                if dist <= rc.maxDist then
                    if rc.visCheck and not AR2_CFG.weapon.wallbang then
                        if not AR2_rageBotVisCheck(model, part, origin) then continue end
                    end

                    local score
                    if rc.ignoreFov then
                        score = dist
                    else
                        local sp, onScreen = cam:WorldToViewportPoint(part.Position)
                        if not (onScreen and sp.Z > 0) then continue end
                        local px = (centre - Vector2.new(sp.X, sp.Y)).Magnitude
                        if px > rc.fov then continue end
                        score = px
                    end
                    if score < bestScore then
                        best, bestPart, bestScore, bestDist = model, part, score, dist
                    end
                end
            end
        end
    end
    return best, bestPart, bestDist
end

local function AR2_doInventoryFire(part)
    if not AR2_lastFireParams or not AR2_originalFireFunction then return end
    local pc = AR2_lastFireParams.pc
    local wi = AR2_lastFireParams.wi
    local wd = AR2_lastFireParams.wd
    local char = AR2_LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local origin = hrp and (hrp.Position + Vector3.new(0,1.5,0)) or AR2_localPosition()
    local dir = (part.Position - origin).Unit

    local saved, spoofed = nil, false
    pcall(function()
        if pc and pc.Character then
            saved = pc.Character.EquippedItem
            if not saved then
                pc.Character.EquippedItem = wi
                spoofed = true
            end
        end
    end)

    local ok, err = pcall(AR2_originalFireFunction,
        AR2_lastFireParams.self, pc, wi, wd,
        origin, dir, math.random(100000, 999999)
    )

    if spoofed then
        pcall(function()
            if pc and pc.Character then
                pc.Character.EquippedItem = saved
            end
        end)
    end
    if not ok then warn("[ragebot] inv fire failed: " .. tostring(err)) end
end

local function AR2_rageBotStep()
    local c = AR2_CFG.rageBot
    if not c.on then return end

    local inner     = AR2_getEquippedInner and AR2_getEquippedInner()
    local gunOut    = inner ~= nil
    local ammo      = AR2_getAmmoLive()
    local reloading = AR2_getReloading()
    local vimOk     = not reloading and (ammo == nil or ammo > 0)
    local invOk     = not reloading and (ammo == nil or (type(ammo) == "number" and ammo > 0))

    if gunOut and not AR2_hasRealWD then
        pcall(function()
            if not AR2_autoSeeding and not AR2_autoSeeded then
                AR2_autoSeeding = true
                task.spawn(function()
                    pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,0,true,game,1) end)
                    task.wait(0.08)
                    pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,0,false,game,1) end)
                    task.wait(0.3)
                    if not AR2_autoSeeded then AR2_autoSeeding = false end
                end)
            end
        end)
    end

    local entity, part, targetDist = AR2_rageBotTarget()
    local hasTarget = entity ~= nil
    AR2_lastTargetDist = targetDist or 0

    if c.forceADS then
        if hasTarget and AR2_lastTargetDist >= c.adsMinDist and vimOk then
            pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,1,true,game,1) end)
            AR2_adsHeld = true
        else
            AR2_releaseADS()
        end
    end

    if c.requireADS then
        local adsActive = AR2_UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2)
            or (c.forceADS and AR2_adsHeld)
        if not adsActive then AR2_releaseVIM() return end
    end

    if hasTarget then
        if gunOut and vimOk then
            pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,0,true,game,1) end)
            AR2_rbHeld = true
        elseif gunOut and not vimOk then
            AR2_releaseVIM()
        elseif c.inventoryShoot and invOk and AR2_hasRealWD
            and AR2_lastFireParams and AR2_originalFireFunction and part then
            AR2_releaseVIM()
            local now = os.clock()
            if now - AR2_rbLastFire >= AR2_getFireInterval(AR2_lastFireParams.wi) then
                AR2_rbLastFire = now
                AR2_doInventoryFire(part)
            end
        else
            AR2_releaseVIM()
        end
    else
        AR2_releaseVIM()
        if c.forceADS then AR2_releaseADS() end
    end
end

function AR2_rageBotSetEnabled(enabled)
    AR2_CFG.rageBot.on = enabled
    if AR2_rageBotConn then
        AR2_rageBotConn:Disconnect()
        AR2_rageBotConn = nil
    end
    AR2_releaseVIM()
    AR2_releaseADS()
    if enabled then
        task.spawn(function()
            if AR2_getEquippedInner and AR2_getEquippedInner() then
                pcall(function()
                    if not AR2_autoSeeding and not AR2_autoSeeded then
                        AR2_autoSeeding = true
                        task.spawn(function()
                            pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,0,true,game,1) end)
                            task.wait(0.08)
                            pcall(function() AR2_VIM:SendMouseButtonEvent(0,0,0,false,game,1) end)
                            task.wait(0.3)
                            if not AR2_autoSeeded then AR2_autoSeeding = false end
                        end)
                    end
                end)
            end
        end)
        AR2_rageBotConn = AR2_RunService.Heartbeat:Connect(function()
            local ok, err = pcall(AR2_rageBotStep)
            if not ok then warn("[ragebot] " .. tostring(err)) end
        end)
    end
end

function AR2_toggleRageBotWorldwide(enabled)
    AR2_CFG.rageBot.ignoreFov = enabled
end

function AR2_toggleRageBotInventoryShoot(enabled)
    AR2_CFG.rageBot.inventoryShoot = enabled
end

function AR2_rageBotPanic()
    pcall(AR2_rageBotSetEnabled, false)
    pcall(function()
        local VIM = game:GetService("VirtualInputManager")
        VIM:SendMouseButtonEvent(0,0,0,false,game,1)
        VIM:SendMouseButtonEvent(0,0,1,false,game,1)
    end)
    AR2_rbHeld, AR2_adsHeld = false, false
    AR2_lastFireParams = nil
    AR2_hasRealWD = false
    AR2_autoSeeding, AR2_autoSeeded = false, false
end

-- ============================================================
-- 23. SPEED HACK
-- ============================================================
AR2_speed = { enabled = false, resets = 0, lastReset = nil }
AR2_speedConn = nil
AR2_lastPosition = nil
AR2_BASE_SPEED = 16
AR2_RESET_JUMP = 25

function AR2_localRig()
    local char = AR2_LocalPlayer.Character
    if not char then return nil, nil, nil end
    return char, char:FindFirstChild("HumanoidRootPart"), char:FindFirstChildWhichIsA("Humanoid")
end

function AR2_wishDirection()
    local cam = Workspace.CurrentCamera
    local dir = Vector3.zero
    if AR2_UserInputService:IsKeyDown(Enum.KeyCode.W) then dir = dir + cam.CFrame.LookVector end
    if AR2_UserInputService:IsKeyDown(Enum.KeyCode.S) then dir = dir - cam.CFrame.LookVector end
    if AR2_UserInputService:IsKeyDown(Enum.KeyCode.D) then dir = dir + cam.CFrame.RightVector end
    if AR2_UserInputService:IsKeyDown(Enum.KeyCode.A) then dir = dir - cam.CFrame.RightVector end
    dir = Vector3.new(dir.X, 0, dir.Z)
    if dir.Magnitude < 0.01 then return nil end
    return dir.Unit
end

function AR2_detectReset(position)
    if not AR2_lastPosition then AR2_lastPosition = position return false end
    local jump = (position - AR2_lastPosition).Magnitude
    AR2_lastPosition = position
    if jump < AR2_RESET_JUMP then return false end
    AR2_speed.resets = AR2_speed.resets + 1
    return true
end

function AR2_stepSpeed(dt)
    local c = AR2_CFG.speed
    local char, root = AR2_localRig()
    if not c.on or not root then AR2_lastPosition = nil return end
    if AR2_detectReset(root.Position) and c.adaptive then
        local reduced = math.max(c.minPct, c.pct - 10)
        if reduced ~= c.pct then c.pct = reduced end
        return
    end
    if c.pct <= 100 then return end
    if c.pulse then
        local onTime = c.pulseOn/100
        local offTime = c.pulseOff/100
        local period = onTime + offTime
        if period > 0 and (os.clock() % period) >= onTime then return end
    end
    local dir = AR2_wishDirection()
    if not dir then return end
    local extra = AR2_BASE_SPEED * (c.pct/100 - 1) * dt
    if extra <= 0 then return end
    if c.collide then
        local params = RaycastParams.new()
        params.FilterType = Enum.RaycastFilterType.Exclude
        params.FilterDescendantsInstances = { char }
        if AR2_Workspace:Raycast(root.Position, dir * (extra + 2), params) then return end
    end
    if c.airborne then
        local grounded = math.abs(root.AssemblyLinearVelocity.Y) < 1
        if grounded then root.CFrame = root.CFrame + Vector3.new(0, 3.5, 0) end
    end
    if c.spoof then
        local folder = char:FindFirstChild("Animator")
        local state = folder and folder:FindFirstChild("MoveState")
        if state and state.Value ~= c.spoofAs then state.Value = c.spoofAs end
    end
    root.CFrame = root.CFrame + dir * extra
    AR2_lastPosition = root.Position
end

function AR2_speed.start()
    if AR2_speedConn then return end
    AR2_speed.enabled = true
    AR2_speedConn = AR2_RunService.RenderStepped:Connect(function(dt)
        local ok, err = pcall(AR2_stepSpeed, dt)
        if not ok then warn("[speed] "..tostring(err)) end
    end)
end

function AR2_speed.stop()
    AR2_speed.enabled = false
    if AR2_speedConn then AR2_speedConn:Disconnect() AR2_speedConn = nil end
end

-- ============================================================
-- 24. FREEZE ZOMBIES
-- ============================================================
AR2_freeze = { running = false, held = 0, near = 0, note = "off", lastUpdate = 0 }
AR2_freezeConn = nil
AR2_frozen = {}

function AR2_freezeAnchorParts(model, record)
    for _, descendant in ipairs(model:GetDescendants()) do
        if descendant:IsA("BasePart") then
            record.anchored[descendant] = descendant.Anchored
            descendant.Anchored = true
        end
    end
end

function AR2_freezeGrab(model, root)
    local record = { cf = root.CFrame, anchored = {} }
    if AR2_CFG.misc.freezeAnchor then pcall(AR2_freezeAnchorParts, model, record) end
    AR2_frozen[model] = record
    return record
end

function AR2_freezeUnanchor(record)
    for part, wasAnchored in pairs(record.anchored) do
        pcall(function() if part.Parent then part.Anchored = wasAnchored end end)
    end
    record.anchored = {}
end

function AR2_freezeRelease(model)
    local record = AR2_frozen[model]
    if not record then return end
    AR2_freezeUnanchor(record)
    AR2_frozen[model] = nil
end

function AR2_freezeReleaseAll()
    for model in pairs(AR2_frozen) do AR2_freezeRelease(model) end
end

function AR2_freezeStep()
    if not AR2_CFG.misc.freeze then
        if next(AR2_frozen) then AR2_freezeReleaseAll() end
        AR2_freeze.held, AR2_freeze.near, AR2_freeze.note = 0, 0, "off"
        return
    end
    local held, near = 0, 0
    local seen = {}
    for _, entry in ipairs(AR2_api.zombies()) do
        if AR2_CFG.misc.freezeAll or entry.distance <= AR2_CFG.misc.freezeRadius then
            near = near + 1
            local model, root = entry.model, entry.root
            if model.Parent and root.Parent then
                seen[model] = true
                local record = AR2_frozen[model] or AR2_freezeGrab(model, root)
                root.CFrame = record.cf
                root.AssemblyLinearVelocity = Vector3.zero
                held = held + 1
            end
        end
    end
    for model in pairs(AR2_frozen) do
        if not seen[model] then AR2_freezeRelease(model) end
    end
    AR2_freeze.held, AR2_freeze.near = held, near
    AR2_freeze.note = AR2_CFG.misc.freezeAll and "whole server" or (AR2_CFG.misc.freezeRadius .. " studs")
end

function AR2_freeze.start()
    if AR2_freezeConn then return end
    AR2_freeze.running = true
    AR2_freezeConn = AR2_RunService.Heartbeat:Connect(function()
        if os.clock() - (AR2_freeze.lastUpdate or 0) >= 0.1 then
            AR2_freeze.lastUpdate = os.clock()
            local ok, err = pcall(AR2_freezeStep)
            if not ok then AR2_freeze.note = "error: "..tostring(err) end
        end
    end)
end

function AR2_freeze.stop()
    AR2_freeze.running = false
    if AR2_freezeConn then AR2_freezeConn:Disconnect() AR2_freezeConn = nil end
    AR2_freezeReleaseAll()
    AR2_freeze.held, AR2_freeze.near, AR2_freeze.note = 0, 0, "off"
end

function AR2_freeze.destroy() AR2_freeze.stop() end

-- ============================================================
-- FIM DO BLOCO C
-- ============================================================
print("[Tetescr hub] Bloco C carregado — ESP + aim + rage + speed + freeze prontos")
if not AR2_success then
    print("[Tetescr hub] aviso: Framework.Libraries.Bullets não estava pronto — features de hook de tiro ficam inativas até a lib carregar")
end
--[[
    Tetescr hub — Bloco D
    TP + noclip + fly + car + visuals + charm + tracers + misc + keybinds + unload
]]

-- ============================================================
-- 25. HELPERS DE VEÍCULO
-- ============================================================
local function AR2_seatedInVehiclesFolder(hum)
    local container = AR2_Workspace:FindFirstChild("Vehicles")
    if not (container and hum) then return false end
    for _, model in ipairs(container:GetChildren()) do
        if model:IsA("Model") then
            for _, d in ipairs(model:GetDescendants()) do
                if (d:IsA("VehicleSeat") or d:IsA("Seat")) and d.Occupant == hum then
                    return true
                end
            end
        end
    end
    return false
end

function AR2_inVehicle()
    local char = AR2_LocalPlayer.Character
    if not char then return false end
    local hum = char:FindFirstChildWhichIsA("Humanoid")
    if hum then
        if hum.Sit == true then return true end
        if hum.SeatPart ~= nil then return true end
    end
    if AR2_vehEquipInVeh == true then return true end
    local ok, seated = pcall(AR2_seatedInVehiclesFolder, hum)
    if ok and seated then return true end
    return false
end

-- ============================================================
-- 26. MAPA / TP CONFIG
-- ============================================================
do
    AR2_MAP_ORIGIN = CFrame.new(Vector3.new(-7349.55, 0, -6187.35), Vector3.new(-7349.55, 0, -6186.35))
    AR2_MAP_SX, AR2_MAP_SZ = -13499.068, -12386.184
    AR2_MAP_IMAGE = "rbxassetid://102357544057594"
    AR2_IMG_W, AR2_IMG_H = 1024, 925
    AR2_mapImageOk = false
    AR2_mapStatus = nil
    AR2_mapLastRoute = ""

    function AR2_mapImageCandidates()
        local id = tostring((AR2_CFG and AR2_CFG.tp and AR2_CFG.tp.mapImageId) or ""):match("%d+")
            or "102357544057594"
        return {
            "rbxassetid://" .. id,
            "http://www.roblox.com/asset/?id=" .. id,
            "rbxthumb://type=Asset&id=" .. id .. "&w=420&h=420",
        }
    end

    function AR2_mapBuildGrid(parent)
        if not parent or parent:FindFirstChild("tetescr_grid") then return end
        local grid = Instance.new("Folder")
        grid.Name = "tetescr_grid"
        grid.Parent = parent
        for i = 1, 9 do
            local vline = Instance.new("Frame")
            vline.BackgroundColor3 = Color3.fromRGB(72, 82, 98)
            vline.BackgroundTransparency = 0.55
            vline.BorderSizePixel = 0
            vline.Size = UDim2.new(0, 1, 1, 0)
            vline.Position = UDim2.fromScale(i / 10, 0)
            vline.ZIndex = 2
            vline.Parent = grid
            local hline = Instance.new("Frame")
            hline.BackgroundColor3 = Color3.fromRGB(72, 82, 98)
            hline.BackgroundTransparency = 0.55
            hline.BorderSizePixel = 0
            hline.Size = UDim2.new(1, 0, 0, 1)
            hline.Position = UDim2.fromScale(0, i / 10)
            hline.ZIndex = 2
            hline.Parent = grid
        end
    end

    function AR2_mapLoadImage()
        if not AR2_mapImg then return end
        task.spawn(function()
            for _, url in ipairs(AR2_mapImageCandidates()) do
                if not AR2_mapImg or not AR2_mapImg.Parent then return end
                pcall(function() AR2_mapImg.Image = url end)
                pcall(function()
                    game:GetService("ContentProvider"):PreloadAsync({ AR2_mapImg })
                end)
                local deadline = os.clock() + 3
                while os.clock() < deadline do
                    local loaded = false
                    pcall(function() loaded = AR2_mapImg.IsLoaded end)
                    if loaded then
                        AR2_mapImageOk = true
                        pcall(function()
                            local g = AR2_mapImg:FindFirstChild("tetescr_grid")
                            if g then g:Destroy() end
                            if AR2_mapStatus then AR2_mapStatus.Text = "" end
                        end)
                        return
                    end
                    task.wait(0.1)
                end
            end
            AR2_mapImageOk = false
            pcall(function()
                AR2_mapBuildGrid(AR2_mapImg)
                if AR2_mapStatus then
                    AR2_mapStatus.Text = "image unavailable — grid mode (clicks still work)"
                end
            end)
        end)
    end

    function AR2_worldToMap(p) local r = AR2_MAP_ORIGIN:PointToObjectSpace(p) return r.X / AR2_MAP_SX, r.Z / AR2_MAP_SZ end
    function AR2_mapToWorld(u, v) return AR2_MAP_ORIGIN:PointToWorldSpace(Vector3.new(u * AR2_MAP_SX, 0, v * AR2_MAP_SZ)) end

    function AR2_groundY(x, z)
        local params = RaycastParams.new()
        params.FilterType = Enum.RaycastFilterType.Exclude
        params.FilterDescendantsInstances = { AR2_LocalPlayer.Character }
        params.IgnoreWater = false
        local hit = AR2_Workspace:Raycast(Vector3.new(x, 5000, z), Vector3.new(0, -12000, 0), params)
        return hit and hit.Position.Y or nil
    end

    AR2_mapGui = nil
    AR2_mapConn = nil
    AR2_mapImg = nil
    AR2_youDot = nil
    AR2_dotPool = {}

    function AR2_buildMap()
        if AR2_mapGui then pcall(function() AR2_mapGui:Destroy() end) end
        AR2_dotPool = {}
        AR2_mapImageOk = false
        local host = (typeof(gethui) == "function" and gethui()) or game:GetService("CoreGui")
        AR2_mapGui = Instance.new("ScreenGui")
        AR2_mapGui.Name = "tetescr_map"
        AR2_mapGui.ResetOnSpawn = false
        AR2_mapGui.IgnoreGuiInset = true
        AR2_mapGui.DisplayOrder = 999998
        AR2_mapGui.Enabled = false
        AR2_mapGui.Parent = host
        local dim = Instance.new("TextButton")
        dim.Size = UDim2.fromScale(1,1)
        dim.BackgroundColor3 = Color3.new(0,0,0)
        dim.BackgroundTransparency = 0.35
        dim.AutoButtonColor = false
        dim.Text = ""
        dim.Parent = AR2_mapGui
        local W = 720
        local H = math.floor(W * AR2_IMG_H / AR2_IMG_W)
        local panel = Instance.new("Frame")
        panel.AnchorPoint = Vector2.new(0.5,0.5)
        panel.Position = UDim2.fromScale(0.5,0.5)
        panel.Size = UDim2.fromOffset(W, H+34)
        panel.BackgroundColor3 = Color3.fromRGB(15,16,20)
        panel.BorderSizePixel = 0
        panel.Parent = AR2_mapGui
        local pc = Instance.new("UICorner") pc.CornerRadius = UDim.new(0,8) pc.Parent = panel
        local title = Instance.new("TextLabel")
        title.BackgroundTransparency = 1
        title.Position = UDim2.fromOffset(12,0)
        title.Size = UDim2.new(1,-80,0,34)
        title.Font = Enum.Font.GothamBold
        title.TextSize = 14
        title.TextXAlignment = Enum.TextXAlignment.Left
        title.TextColor3 = Color3.fromRGB(225,225,235)
        title.Text = "Map"
        title.Parent = panel
        local close = Instance.new("TextButton")
        close.Size = UDim2.fromOffset(26,22)
        close.Position = UDim2.new(1,-32,0,6)
        close.BackgroundColor3 = Color3.fromRGB(40,42,52)
        close.Text = "X"
        close.Font = Enum.Font.GothamBold
        close.TextSize = 13
        close.TextColor3 = Color3.fromRGB(230,120,120)
        close.BorderSizePixel = 0
        close.Parent = panel
        local cc = Instance.new("UICorner") cc.CornerRadius = UDim.new(0,5) cc.Parent = close
        AR2_mapStatus = Instance.new("TextLabel")
        AR2_mapStatus.BackgroundTransparency = 1
        AR2_mapStatus.Position = UDim2.fromOffset(12,16)
        AR2_mapStatus.Size = UDim2.new(1,-80,0,16)
        AR2_mapStatus.Font = Enum.Font.Gotham
        AR2_mapStatus.TextSize = 11
        AR2_mapStatus.TextXAlignment = Enum.TextXAlignment.Left
        AR2_mapStatus.TextColor3 = Color3.fromRGB(235,180,110)
        AR2_mapStatus.Text = "loading map image..."
        AR2_mapStatus.Parent = panel
        AR2_mapImg = Instance.new("ImageButton")
        AR2_mapImg.Position = UDim2.fromOffset(0,34)
        AR2_mapImg.Size = UDim2.fromOffset(W,H)
        AR2_mapImg.BackgroundColor3 = Color3.fromRGB(20,24,30)
        AR2_mapImg.ScaleType = Enum.ScaleType.Stretch
        AR2_mapImg.AutoButtonColor = false
        AR2_mapImg.BorderSizePixel = 0
        AR2_mapImg.Parent = panel
        local ic = Instance.new("UICorner") ic.CornerRadius = UDim.new(0,6) ic.Parent = AR2_mapImg
        AR2_mapLoadImage()
        AR2_youDot = Instance.new("Frame")
        AR2_youDot.AnchorPoint = Vector2.new(0.5,0.5)
        AR2_youDot.Size = UDim2.fromOffset(9,9)
        AR2_youDot.BackgroundColor3 = Color3.fromRGB(255,220,60)
        AR2_youDot.BorderSizePixel = 0
        AR2_youDot.ZIndex = 5
        AR2_youDot.Parent = AR2_mapImg
        local yc = Instance.new("UICorner") yc.CornerRadius = UDim.new(1,0) yc.Parent = AR2_youDot
        dim.MouseButton1Click:Connect(function() AR2_tp.toggleMap(false) end)
        close.MouseButton1Click:Connect(function() AR2_tp.toggleMap(false) end)

        AR2_mapImg.Activated:Connect(function()
            local mouse = AR2_UserInputService:GetMouseLocation()
            local abs, sz = AR2_mapImg.AbsolutePosition, AR2_mapImg.AbsoluteSize
            if sz.X <= 0 or sz.Y <= 0 then return end
            local u = math.clamp((mouse.X - abs.X) / sz.X, 0, 1)
            local v = math.clamp((mouse.Y - abs.Y) / sz.Y, 0, 1)
            local w = AR2_mapToWorld(u, v)
            local y = AR2_groundY(w.X, w.Z)
            local dest = Vector3.new(w.X, (y or 200) + 6, w.Z)
            local label = string.format("map (%.0f, %.0f)", w.X, w.Z)

            AR2_tp.toggleMap(false)

            if not (AR2_carNoclipTP and AR2_carNoclipTP.toPosition) then
                AR2_tp.lastResult = label .. " -- vehicle TP unavailable"
                AR2_notify("Vehicle TP unavailable")
                return
            end

            if not AR2_inVehicle() then
                AR2_tp.lastResult = label .. " -- not in a vehicle"
                AR2_notify("Get in a vehicle first — map TP flies your car there", 5)
                return
            end

            AR2_mapLastRoute = "vehicle"
            AR2_tp.lastResult = label .. " -- vehicle route"
            AR2_notify(string.format("Flying to %.0f, %.0f", w.X, w.Z), 3)

            task.spawn(function()
                AR2_carNoclipTP.lastResult = ""
                pcall(function() AR2_carNoclipTP.toPosition(dest, label) end)
                local deadline = os.clock() + (AR2_CFG.car.noclipTPMaxSeconds or 15) + 5
                while os.clock() < deadline do
                    if AR2_carNoclipTP.lastResult ~= "" and not AR2_carNoclipTP.busy then break end
                    task.wait(0.15)
                end
                if AR2_carNoclipTP.lastResult ~= "" then
                    AR2_notify(AR2_carNoclipTP.lastResult, 4)
                end
            end)
        end)
    end

    local function AR2_mapDot(i, size, color, zi)
        local d = AR2_dotPool[i]
        if not d then
            d = Instance.new("Frame")
            d.AnchorPoint = Vector2.new(0.5,0.5)
            d.BorderSizePixel = 0
            d.Parent = AR2_mapImg
            local dc = Instance.new("UICorner") dc.CornerRadius = UDim.new(1,0) dc.Parent = d
            local lbl = Instance.new("TextLabel")
            lbl.Name = "tag"
            lbl.AnchorPoint = Vector2.new(0.5,1)
            lbl.Position = UDim2.new(0.5,0,0,-2)
            lbl.Size = UDim2.fromOffset(120,12)
            lbl.BackgroundTransparency = 1
            lbl.Font = Enum.Font.GothamBold
            lbl.TextSize = 10
            lbl.TextStrokeTransparency = 0.4
            lbl.TextColor3 = Color3.fromRGB(240,240,245)
            lbl.Visible = false
            lbl.ZIndex = 6
            lbl.Parent = d
            AR2_dotPool[i] = d
        end
        d.Size = UDim2.fromOffset(size, size)
        d.BackgroundColor3 = color
        d.ZIndex = zi
        d.Visible = true
        return d
    end

    function AR2_refreshDots()
        if not AR2_mapImg or not AR2_mapImg.Parent then return end
        local root = AR2_LocalPlayer.Character and AR2_LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        local myPos = root and root.Position or nil
        if root then
            local u, v = AR2_worldToMap(root.Position)
            AR2_youDot.Position = UDim2.fromScale(u, v)
            AR2_youDot.Visible = true
        else
            AR2_youDot.Visible = false
        end

        local i = 0
        local showNames = AR2_CFG.radar and AR2_CFG.radar.names
        local showDist  = AR2_CFG.radar and AR2_CFG.radar.dist

        for _, pl in ipairs(AR2_Players:GetPlayers()) do
            local r = pl ~= AR2_LocalPlayer and pl.Character and pl.Character:FindFirstChild("HumanoidRootPart")
            if r then
                i = i + 1
                local d = AR2_mapDot(i, 7, AR2_COLORS.player or Color3.fromRGB(255,90,90), 4)
                local u, v = AR2_worldToMap(r.Position)
                d.Position = UDim2.fromScale(u, v)
                local tag = d:FindFirstChild("tag")
                if tag then
                    if showNames or showDist then
                        local txt = showNames and pl.Name or ""
                        if showDist and myPos then
                            local dd = math.floor((r.Position - myPos).Magnitude)
                            txt = (txt ~= "" and (txt .. "  ") or "") .. dd .. "m"
                        end
                        tag.Text = txt
                        tag.TextColor3 = AR2_COLORS.player or Color3.fromRGB(255,120,120)
                        tag.Visible = true
                    else
                        tag.Visible = false
                    end
                end
            end
        end

        local vehCfg = AR2_CFG.radar and AR2_CFG.radar.vehicles
        if vehCfg and vehCfg.on then
            local container = AR2_Workspace:FindFirstChild("Vehicles")
            if container then
                for _, model in ipairs(container:GetChildren()) do
                    if model:IsA("Model") then
                        local base = AR2_carBase(model)
                        if base then
                            i = i + 1
                            local d = AR2_mapDot(i, 6, AR2_COLORS.vehicle or Color3.fromRGB(90,200,255), 3)
                            local u, v = AR2_worldToMap(base.Position)
                            d.Position = UDim2.fromScale(u, v)
                            local tag = d:FindFirstChild("tag")
                            if tag then
                                if vehCfg.names or vehCfg.dist then
                                    local txt = vehCfg.names and model.Name or ""
                                    if vehCfg.dist and myPos then
                                        local dd = math.floor((base.Position - myPos).Magnitude)
                                        txt = (txt ~= "" and (txt .. "  ") or "") .. dd .. "m"
                                    end
                                    tag.Text = txt
                                    tag.TextColor3 = AR2_COLORS.vehicle or Color3.fromRGB(120,210,255)
                                    tag.Visible = true
                                else
                                    tag.Visible = false
                                end
                            end
                        end
                    end
                end
            end
        end

        for j = i+1, #AR2_dotPool do
            AR2_dotPool[j].Visible = false
        end
    end

    function AR2_tp.toggleMap(on)
        if not AR2_mapGui or not AR2_mapGui.Parent then AR2_buildMap() end
        if on == nil then on = not AR2_mapGui.Enabled end
        AR2_mapGui.Enabled = on
        if on then
            AR2_refreshDots()
            if not AR2_mapConn then
                local acc = 0
                AR2_mapConn = AR2_RunService.RenderStepped:Connect(function(dt)
                    acc = acc + dt
                    if acc >= 0.2 then acc = 0 pcall(AR2_refreshDots) end
                end)
            end
        elseif AR2_mapConn then
            AR2_mapConn:Disconnect()
            AR2_mapConn = nil
        end
    end
end

-- ============================================================
-- 27. NOTIFICAÇÕES
-- ============================================================
AR2_Notif = nil

function AR2_notify(content, dur)
    local shown = false
    pcall(function()
        if AR2_Notif and AR2_Notif.new then
            AR2_Notif.new({ Title = "Tetescr hub", Content = tostring(content), Duration = dur or 4 })
            shown = true
        end
    end)
    if not shown then warn("[Tetescr hub] " .. tostring(content)) end
end

-- ============================================================
-- 28. RADAR
-- ============================================================
AR2_radar = { running = false, layer = "off", players = 0, drawn = 0, sample = "" }
do
    local MAP_ORIGIN = CFrame.new(Vector3.new(-7349.55, 0, -6187.35), Vector3.new(-7349.55, 0, -6186.35))
    local MAP_SX, MAP_SZ = -13499.068, -12386.184
    local function worldToMap(position)
        local rel = MAP_ORIGIN:PointToObjectSpace(position)
        return rel.X / MAP_SX, rel.Z / MAP_SZ
    end
    local RADAR_COLOR = { normal = Color3.fromRGB(120,190,255), armed = Color3.fromRGB(255,150,50), localP = Color3.fromRGB(255,220,60), veh = Color3.fromRGB(90,255,140), corpse = Color3.fromRGB(200,200,205) }
    local radarConn, radarDots = nil, {}
    local radarLayer, radarMarker, radarNextScan = nil, nil, 0

    local function radarRendered(gui)
        if gui.AbsoluteSize.X <= 0 then return false end
        local node = gui
        while node do
            if node:IsA("ScreenGui") then return node.Enabled end
            if node:IsA("GuiObject") and not node.Visible then return false end
            node = node.Parent
        end
        return true
    end

    local function radarMarkerLayer()
        if radarMarker and radarMarker.Parent and radarRendered(radarMarker) then
            return radarLayer, radarMarker, "ok"
        end
        local now = os.clock()
        if now < radarNextScan then return radarLayer, radarMarker, "map not open" end
        radarNextScan = now + 2.0
        radarLayer, radarMarker = nil, nil
        local gui = AR2_LocalPlayer:FindFirstChild("PlayerGui")
        if not gui then return nil, nil, "no PlayerGui" end

        local anyMarker = false
        for _, descendant in ipairs(gui:GetDescendants()) do
            if descendant.Name == "LocalMarker" and descendant:IsA("GuiObject") then
                anyMarker = true
                if radarRendered(descendant) then
                    radarLayer, radarMarker = descendant.Parent, descendant
                    return descendant.Parent, descendant, "ok"
                end
            end
        end
        return nil, nil, anyMarker and "map not open" or "no LocalMarker"
    end

    local function radarMakeDot(zbase)
        local dot = Instance.new("Frame")
        dot.Name = "tetescr_dot"
        dot.AnchorPoint = Vector2.new(0.5,0.5)
        dot.Size = UDim2.fromOffset(12,12)
        dot.BorderSizePixel = 0
        dot.ZIndex = zbase + 5
        local round = Instance.new("UICorner") round.CornerRadius = UDim.new(1,0) round.Parent = dot
        local edge = Instance.new("UIStroke") edge.Color = Color3.fromRGB(0,0,0) edge.Thickness = 1.5 edge.Transparency = 0.25 edge.Parent = dot
        local name = Instance.new("TextLabel")
        name.Name = "Name"
        name.BackgroundTransparency = 1
        name.AnchorPoint = Vector2.new(0,0.5)
        name.Position = UDim2.fromOffset(10,0)
        name.Size = UDim2.fromOffset(200,28)
        name.Font = Enum.Font.GothamBold
        name.TextSize = 12
        name.TextXAlignment = Enum.TextXAlignment.Left
        name.TextStrokeTransparency = 0.4
        name.ZIndex = zbase + 6
        name.Parent = dot
        return dot
    end

    local function radarColorFor(entry)
        if entry.isLocal then return RADAR_COLOR.localP end
        if AR2_CFG.radar.armedColor and entry.armed then return RADAR_COLOR.armed end
        return RADAR_COLOR.normal
    end

    local function radarClearDots()
        for name, dot in pairs(radarDots) do
            dot:Destroy()
            radarDots[name] = nil
        end
    end

    local function radarStep()
        if not AR2_CFG.radar.on then
            if next(radarDots) then radarClearDots() end
            AR2_radar.layer, AR2_radar.players, AR2_radar.drawn = "disabled", 0, 0
            return
        end
        local parent, localMarker, why = radarMarkerLayer()
        AR2_radar.layer = why
        if not parent then
            if next(radarDots) then radarClearDots() end
            AR2_radar.players, AR2_radar.drawn = 0, 0
            return
        end
        local zbase = localMarker and localMarker.ZIndex or 10
        local seen, drawn = {}, 0

        local function place(key, worldPos, colour, labelText)
            seen[key] = true
            local dot = radarDots[key]
            if not dot or dot.Parent ~= parent then
                if dot then dot:Destroy() end
                dot = radarMakeDot(zbase)
                dot.Parent = parent
                radarDots[key] = dot
            end
            local x, z = worldToMap(worldPos)
            dot.Position = UDim2.fromScale(x, z)
            dot.BackgroundColor3 = colour
            local label = dot:FindFirstChild("Name")
            if label then
                label.Text = labelText or ""
                label.Visible = labelText ~= nil and labelText ~= ""
                label.TextColor3 = colour
            end
            drawn = drawn + 1
        end

        local live = AR2_api.players(true)
        AR2_radar.players = #live
        for _, e in ipairs(live) do
            if (AR2_CFG.radar.localToo or not e.isLocal) and e.root then
                local parts = {}
                if AR2_CFG.radar.names then parts[#parts+1] = e.name end
                if AR2_CFG.radar.weapon and e.item then parts[#parts+1] = "["..e.item.."]" end
                if AR2_CFG.radar.dist then parts[#parts+1] = string.format("%.0fs", e.distance) end
                place("p:"..e.name, e.root.Position, radarColorFor(e), #parts>0 and table.concat(parts,"  ") or nil)
            end
        end

        if AR2_CFG.radar.vehicles and AR2_CFG.radar.vehicles.on then
            for _, e in ipairs(AR2_api.vehicles()) do
                if e.root then
                    local parts = {}
                    if AR2_CFG.radar.vehicles.names then parts[#parts+1] = e.name end
                    if AR2_CFG.radar.vehicles.dist then parts[#parts+1] = string.format("%.0fs", e.distance) end
                    place("v:"..e.model:GetFullName(), e.root.Position, RADAR_COLOR.veh, #parts>0 and table.concat(parts,"  ") or nil)
                end
            end
        end

        if AR2_CFG.radar.corpses and AR2_CFG.radar.corpses.on then
            for _, e in ipairs(AR2_api.corpses()) do
                local pos = e.position or (e.root and e.root.Position)
                if pos then
                    local parts = {}
                    if AR2_CFG.radar.corpses.names then parts[#parts+1] = e.name end
                    if AR2_CFG.radar.corpses.dist then parts[#parts+1] = string.format("%.0fs", e.distance) end
                    place("c:"..tostring(e.name), pos, RADAR_COLOR.corpse, #parts>0 and table.concat(parts,"  ") or nil)
                end
            end
        end

        AR2_radar.drawn = drawn
        for key, dot in pairs(radarDots) do
            if not seen[key] then dot:Destroy() radarDots[key] = nil end
        end
    end

    function AR2_radar.start()
        if radarConn then return end
        AR2_radar.running = true
        AR2_radar.layer = "starting"
        radarConn = AR2_RunService.RenderStepped:Connect(function()
            local ok, err = pcall(radarStep)
            if not ok then AR2_radar.layer = "error: "..tostring(err) end
        end)
    end

    function AR2_radar.stop()
        AR2_radar.running = false
        if radarConn then radarConn:Disconnect() radarConn = nil end
        radarClearDots()
        AR2_radar.layer, AR2_radar.players, AR2_radar.drawn = "off", 0, 0
    end

    function AR2_radar.destroy()
        AR2_radar.stop()
        radarLayer, radarMarker = nil, nil
        radarNextScan = 0
    end
end

-- ============================================================
-- 29. MINIMAP
-- ============================================================
AR2_minimapEnabled = false
AR2_minimapGui = nil
AR2_minimapImg = nil
AR2_minimapYouDot = nil
AR2_minimapDots = {}
AR2_minimapConn = nil

function AR2_buildMinimap()
    local host = (typeof(gethui) == "function" and gethui()) or game:GetService("CoreGui")
    AR2_minimapGui = Instance.new("ScreenGui")
    AR2_minimapGui.Name = "tetescr_minimap"
    AR2_minimapGui.ResetOnSpawn = false
    AR2_minimapGui.IgnoreGuiInset = true
    AR2_minimapGui.DisplayOrder = 999997
    AR2_minimapGui.Enabled = AR2_minimapEnabled
    AR2_minimapGui.Parent = host
    AR2_minimapImg = Instance.new("ImageButton")
    AR2_minimapImg.Size = UDim2.fromOffset(AR2_CFG.minimap.size, AR2_CFG.minimap.size)
    AR2_minimapImg.Position = UDim2.fromOffset(AR2_CFG.minimap.x, AR2_CFG.minimap.y)
    AR2_minimapImg.BackgroundColor3 = Color3.fromRGB(20,24,30)
    AR2_minimapImg.Image = AR2_MAP_IMAGE
    AR2_minimapImg.ScaleType = Enum.ScaleType.Stretch
    AR2_minimapImg.AutoButtonColor = false
    AR2_minimapImg.BorderSizePixel = 0
    AR2_minimapImg.Parent = AR2_minimapGui
    local corner = Instance.new("UICorner") corner.CornerRadius = UDim.new(0,6) corner.Parent = AR2_minimapImg
    AR2_minimapYouDot = Instance.new("Frame")
    AR2_minimapYouDot.AnchorPoint = Vector2.new(0.5,0.5)
    AR2_minimapYouDot.Size = UDim2.fromOffset(7,7)
    AR2_minimapYouDot.BackgroundColor3 = Color3.fromRGB(255,220,60)
    AR2_minimapYouDot.BorderSizePixel = 0
    AR2_minimapYouDot.ZIndex = 5
    AR2_minimapYouDot.Parent = AR2_minimapImg
end

function AR2_updateMinimap()
    if not AR2_minimapEnabled then
        if AR2_minimapGui then AR2_minimapGui.Enabled = false end
        return
    end
    if not AR2_minimapGui or not AR2_minimapGui.Parent then AR2_buildMinimap() end
    AR2_minimapGui.Enabled = true
    AR2_minimapImg.Size = UDim2.fromOffset(AR2_CFG.minimap.size, AR2_CFG.minimap.size)
    AR2_minimapImg.Position = UDim2.fromOffset(AR2_CFG.minimap.x, AR2_CFG.minimap.y)
    local root = AR2_LocalPlayer.Character and AR2_LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if root then
        local u, v = AR2_worldToMap(root.Position)
        AR2_minimapYouDot.Position = UDim2.fromScale(u, v)
        AR2_minimapYouDot.Visible = true
    else
        AR2_minimapYouDot.Visible = false
    end
    local i = 0
    for _, pl in ipairs(AR2_Players:GetPlayers()) do
        if pl == AR2_LocalPlayer then continue end
        local pRoot = pl.Character and pl.Character:FindFirstChild("HumanoidRootPart")
        if pRoot then
            i = i + 1
            local dot = AR2_minimapDots[i]
            if not dot then
                dot = Instance.new("Frame")
                dot.AnchorPoint = Vector2.new(0.5,0.5)
                dot.Size = UDim2.fromOffset(5,5)
                dot.BackgroundColor3 = Color3.fromRGB(255,90,90)
                dot.BorderSizePixel = 0
                dot.ZIndex = 4
                dot.Parent = AR2_minimapImg
                AR2_minimapDots[i] = dot
            end
            local u, v = AR2_worldToMap(pRoot.Position)
            dot.Position = UDim2.fromScale(u, v)
            dot.Visible = true
        end
    end
    for j = i+1, #AR2_minimapDots do AR2_minimapDots[j].Visible = false end
end

function AR2_minimapStart()
    if AR2_minimapConn then return end
    AR2_minimapConn = AR2_RunService.RenderStepped:Connect(function()
        if not AR2_minimapEnabled then return end
        AR2_updateMinimap()
    end)
end

function AR2_minimapStop()
    if AR2_minimapConn then AR2_minimapConn:Disconnect() AR2_minimapConn = nil end
end

function AR2_minimapUpdate()
    if AR2_CFG.minimap.on and not AR2_minimapConn then AR2_minimapStart()
    elseif not AR2_CFG.minimap.on and AR2_minimapConn then AR2_minimapStop() end
end

-- ============================================================
-- 30. VISUALS
-- ============================================================
AR2_visuals = { running = false, note = "off" }

function AR2_atmosphere() return AR2_Lighting:FindFirstChildOfClass("Atmosphere") end

AR2_ORIG = {
    Brightness = AR2_Lighting.Brightness, Ambient = AR2_Lighting.Ambient,
    OutdoorAmbient = AR2_Lighting.OutdoorAmbient, GlobalShadows = AR2_Lighting.GlobalShadows,
    ExposureCompensation = AR2_Lighting.ExposureCompensation, FogEnd = AR2_Lighting.FogEnd,
    FogStart = AR2_Lighting.FogStart, ClockTime = AR2_Lighting.ClockTime,
    AtmosphereDensity = (AR2_atmosphere() and AR2_atmosphere().Density) or nil,
}

local AR2_origColorCorrection = nil
local AR2_addedColorCorrection = false
local AR2_origBloom = nil
local AR2_addedBloom = false

local function AR2_snapshotPostFX()
    local existingCC = AR2_Lighting:FindFirstChildOfClass("ColorCorrectionEffect")
    if existingCC then
        AR2_origColorCorrection = { inst = existingCC, Saturation = existingCC.Saturation, Contrast = existingCC.Contrast, Brightness = existingCC.Brightness, Enabled = existingCC.Enabled }
    end
    local existingBloom = AR2_Lighting:FindFirstChildOfClass("BloomEffect")
    if existingBloom then
        AR2_origBloom = { inst = existingBloom, Intensity = existingBloom.Intensity, Size = existingBloom.Size, Threshold = existingBloom.Threshold, Enabled = existingBloom.Enabled }
    end
end
AR2_snapshotPostFX()

function AR2_applyFullbright()
    AR2_Lighting.Brightness = AR2_CFG.visuals.brightness
    AR2_Lighting.Ambient = Color3.fromRGB(180, 180, 180)
    AR2_Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 200)
    AR2_Lighting.GlobalShadows = false
    AR2_Lighting.ExposureCompensation = 0.45
    local cc = AR2_origColorCorrection and AR2_origColorCorrection.inst or AR2_Lighting:FindFirstChildOfClass("ColorCorrectionEffect")
    if not cc or not cc.Parent then
        cc = Instance.new("ColorCorrectionEffect")
        cc.Name = "AR2_CC"
        cc.Parent = AR2_Lighting
        AR2_addedColorCorrection = true
    end
    cc.Saturation = 0.25 cc.Contrast = 0.10 cc.Brightness = 0.02 cc.Enabled = true
    local bloom = AR2_origBloom and AR2_origBloom.inst or AR2_Lighting:FindFirstChildOfClass("BloomEffect")
    if not bloom or not bloom.Parent then
        bloom = Instance.new("BloomEffect")
        bloom.Name = "AR2_Bloom"
        bloom.Parent = AR2_Lighting
        AR2_addedBloom = true
    end
    bloom.Intensity = 0.20 bloom.Size = 22 bloom.Threshold = 0.95 bloom.Enabled = true
end

function AR2_restoreFullbright()
    AR2_Lighting.Brightness          = AR2_ORIG.Brightness
    AR2_Lighting.Ambient             = AR2_ORIG.Ambient
    AR2_Lighting.OutdoorAmbient      = AR2_ORIG.OutdoorAmbient
    AR2_Lighting.GlobalShadows       = AR2_ORIG.GlobalShadows
    AR2_Lighting.ExposureCompensation = AR2_ORIG.ExposureCompensation
    if AR2_addedColorCorrection then
        local cc = AR2_Lighting:FindFirstChild("AR2_CC")
        if cc then cc:Destroy() end
        AR2_addedColorCorrection = false
    elseif AR2_origColorCorrection then
        local o = AR2_origColorCorrection
        o.inst.Saturation = o.Saturation
        o.inst.Contrast = o.Contrast
        o.inst.Brightness = o.Brightness
        o.inst.Enabled = o.Enabled
    end
    if AR2_addedBloom then
        local bloom = AR2_Lighting:FindFirstChild("AR2_Bloom")
        if bloom then bloom:Destroy() end
        AR2_addedBloom = false
    elseif AR2_origBloom then
        local o = AR2_origBloom
        o.inst.Intensity = o.Intensity
        o.inst.Size = o.Size
        o.inst.Threshold = o.Threshold
        o.inst.Enabled = o.Enabled
    end
end

function AR2_applyNoFog()
    AR2_Lighting.FogEnd = 1e6
    AR2_Lighting.FogStart = 0
    local air = AR2_atmosphere()
    if air then air.Density = 0.0001 end
end

function AR2_restoreFog()
    AR2_Lighting.FogEnd = AR2_ORIG.FogEnd
    AR2_Lighting.FogStart = AR2_ORIG.FogStart
    local air = AR2_atmosphere()
    if air and AR2_ORIG.AtmosphereDensity then air.Density = AR2_ORIG.AtmosphereDensity end
end

AR2_visualsConn = nil

function AR2_visualsStep()
    local c = AR2_CFG.visuals
    if c.fullbright then AR2_applyFullbright() end
    if c.nofog then AR2_applyNoFog() end
    if c.setTime then AR2_Lighting.ClockTime = c.time end
end

function AR2_visuals.apply()
    local c = AR2_CFG.visuals
    if not c.fullbright then AR2_restoreFullbright() end
    if not c.nofog then AR2_restoreFog() end
    if not c.setTime then AR2_Lighting.ClockTime = AR2_ORIG.ClockTime end
    if c.fullbright or c.nofog or c.setTime then
        if not AR2_visualsConn then
            AR2_visualsConn = AR2_RunService.RenderStepped:Connect(function()
                local ok, err = pcall(AR2_visualsStep)
                if not ok then AR2_visuals.note = "error: "..tostring(err) end
            end)
        end
        AR2_visuals.running = true
        local parts_ = {}
        if c.fullbright then parts_[#parts_+1] = "fullbright" end
        if c.nofog then parts_[#parts_+1] = "nofog" end
        if c.setTime then parts_[#parts_+1] = string.format("%dh", c.time) end
        AR2_visuals.note = table.concat(parts_, ", ")
    else
        if AR2_visualsConn then AR2_visualsConn:Disconnect() AR2_visualsConn = nil end
        AR2_visuals.running = false
        AR2_visuals.note = "off"
    end
end

function AR2_visuals.destroy()
    if AR2_visualsConn then AR2_visualsConn:Disconnect() AR2_visualsConn = nil end
    AR2_visuals.running = false
    AR2_restoreFullbright()
    AR2_restoreFog()
    AR2_Lighting.ClockTime = AR2_ORIG.ClockTime
    AR2_visuals.note = "off"
end

-- ============================================================
-- 31. CHARM
-- ============================================================
AR2_charmHighlights = {}
function AR2_getCharmColor()
    if AR2_charmMode == "BlackWhite" then
        local gray = AR2_charmGray / 100
        return Color3.fromRGB(gray*255, gray*255, gray*255)
    else
        return Color3.fromHSV(AR2_charmHue/360, 1, 1)
    end
end

function AR2_applyCharmToCharacter(char)
    if not char then return end
    local existing = AR2_charmHighlights[char]
    if existing then existing:Destroy() AR2_charmHighlights[char] = nil end
    if AR2_charmEnabled then
        local fillColor = AR2_getCharmColor()
        local outlineColor = Color3.new(1,1,1)
        local fillTransparency = 0.3
        if AR2_charmMode == "BlackWhite" then outlineColor = Color3.new(0,0,0) end
        local highlight = Instance.new("Highlight")
        highlight.FillColor = fillColor
        highlight.OutlineColor = outlineColor
        highlight.FillTransparency = fillTransparency
        highlight.OutlineTransparency = 0
        highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        highlight.Adornee = char
        highlight.Parent = char
        AR2_charmHighlights[char] = highlight
    end
end

function AR2_applyCharm(char)
    AR2_applyCharmToCharacter(char)
    if AR2_CFG.misc.globalCharm then
        for _, player in ipairs(AR2_Players:GetPlayers()) do
            if player ~= AR2_LocalPlayer and player.Character then
                AR2_applyCharmToCharacter(player.Character)
            end
        end
    end
end

function AR2_toggleCharm(enabled)
    AR2_charmEnabled = enabled
    if enabled then
        AR2_applyCharm(AR2_LocalPlayer.Character)
        if AR2_charmRainbow and not AR2_charmRainbowConn then
            AR2_charmRainbowConn = AR2_RunService.Heartbeat:Connect(function()
                if not AR2_charmEnabled or not AR2_charmRainbow then
                    if AR2_charmRainbowConn then AR2_charmRainbowConn:Disconnect() AR2_charmRainbowConn = nil end
                    return
                end
                AR2_charmHue = (AR2_charmHue + 0.5) % 360
                local col = Color3.fromHSV(AR2_charmHue/360, 1, 1)
                for _, highlight in pairs(AR2_charmHighlights) do highlight.FillColor = col end
            end)
        end
    else
        for _, highlight in pairs(AR2_charmHighlights) do highlight:Destroy() end
        AR2_charmHighlights = {}
        if AR2_charmRainbowConn then AR2_charmRainbowConn:Disconnect() AR2_charmRainbowConn = nil end
    end
end

function AR2_setCharmMode(mode)
    AR2_charmMode = mode
    AR2_CFG.misc.charmMode = mode
    if AR2_charmEnabled then AR2_applyCharm(AR2_LocalPlayer.Character) end
end

function AR2_setCharmHue(hue)
    AR2_charmHue = hue % 360
    AR2_CFG.misc.charmHue = AR2_charmHue
    if AR2_charmEnabled and AR2_charmMode == "Color" then
        for _, highlight in pairs(AR2_charmHighlights) do
            highlight.FillColor = Color3.fromHSV(AR2_charmHue/360,1,1)
        end
    end
end

function AR2_setCharmGray(gray)
    AR2_charmGray = gray
    AR2_CFG.misc.charmGray = gray
    if AR2_charmEnabled and AR2_charmMode == "BlackWhite" then
        local g = gray/100
        for _, highlight in pairs(AR2_charmHighlights) do
            highlight.FillColor = Color3.fromRGB(g*255,g*255,g*255)
        end
    end
end

function AR2_toggleCharmRainbow(enabled)
    AR2_charmRainbow = enabled
    AR2_CFG.misc.charmRainbow = enabled
    if enabled and AR2_charmEnabled then
        if not AR2_charmRainbowConn then
            AR2_charmRainbowConn = AR2_RunService.Heartbeat:Connect(function()
                if not AR2_charmEnabled or not AR2_charmRainbow then
                    if AR2_charmRainbowConn then AR2_charmRainbowConn:Disconnect() AR2_charmRainbowConn = nil end
                    return
                end
                AR2_charmHue = (AR2_charmHue + 0.5) % 360
                local col = Color3.fromHSV(AR2_charmHue/360, 1, 1)
                for _, highlight in pairs(AR2_charmHighlights) do highlight.FillColor = col end
            end)
        end
    else
        if AR2_charmRainbowConn then AR2_charmRainbowConn:Disconnect() AR2_charmRainbowConn = nil end
    end
end

-- ============================================================
-- 32. TRACERS
-- ============================================================
AR2_tracers = {}
AR2_tracersConn = nil

AR2_TRACER_STYLES = {
    Plain     = "",
    Beam      = "rbxassetid://12781852245",
    Lightning = "rbxassetid://446111271",
    Trail     = "rbxassetid://6419989824",
    Zigzag    = "rbxassetid://1274380363",
    Heartrate = "rbxassetid://5830549480",
    Chain     = "rbxassetid://9632168658",
    Glitch    = "rbxassetid://8089467613",
    Swirl     = "rbxassetid://5638168605",
    Classic   = "rbxassetid://2382169232",
}

AR2_TRACER_STYLE_NAMES = {
    "Beam","Plain","Classic","Lightning","Trail","Zigzag",
    "Heartrate","Chain","Glitch","Swirl","3D Part",
}

do
local AR2_beamPool = {}
local AR2_TRACER_SEQ_CLEAR = NumberSequence.new(0)
local AR2_TRACER_FADE_LUT = {}
for i = 0, 16 do AR2_TRACER_FADE_LUT[i] = NumberSequence.new(i / 16) end
local AR2_lastTracerCol, AR2_lastTracerSeq, AR2_tracerColDirty = nil, nil, false

local function AR2_tracerColor()
    if AR2_CFG.misc.tracerRainbow then
        return Color3.fromHSV((tick() * 1000 % 360) / 360, 1, 1)
    end
    return Color3.fromHSV((AR2_CFG.misc.tracerHue or 0) / 360, 1, 1)
end

local function AR2_killTracer(t)
    pcall(function()
        if t.Beam then t.Beam:Destroy() end
        if t.Near then t.Near:Destroy() end
        if t.Far then t.Far:Destroy() end
    end)
end

local function AR2_freeTracer(t, broken)
    if broken or not t.Beam or t.Beam.Parent == nil then AR2_killTracer(t) return end
    t.Beam.Enabled = false
    if #AR2_beamPool < (AR2_CFG.misc.tracerMax or 48) then
        AR2_beamPool[#AR2_beamPool + 1] = t
    else
        AR2_killTracer(t)
    end
end

function AR2_tracersClear()
    for _, t in ipairs(AR2_tracers) do AR2_killTracer(t) end
    for _, t in ipairs(AR2_beamPool) do AR2_killTracer(t) end
    AR2_tracers, AR2_beamPool = {}, {}
    AR2_lastTracerCol, AR2_lastTracerSeq, AR2_tracerColDirty = nil, nil, false
end

local function AR2_newTracer()
    local terrain = AR2_Workspace.Terrain
    if not terrain then return nil end
    local near = Instance.new("Attachment") near.Parent = terrain
    local far  = Instance.new("Attachment") far.Parent  = terrain
    local b = Instance.new("Beam")
    b.Name = "TETESCR_Tracer"
    b.Attachment0, b.Attachment1 = near, far
    b.FaceCamera, b.LightInfluence = true, 0
    b.Parent = terrain
    return { Beam = b, Near = near, Far = far, P = 0, V = 0 }
end

local function AR2_makeBeamTracer(a, b2)
    if typeof(a) ~= "Vector3" or typeof(b2) ~= "Vector3" then return end
    local maxT = math.max(1, AR2_CFG.misc.tracerMax or 48)
    while #AR2_tracers >= maxT do
        AR2_freeTracer(table.remove(AR2_tracers, 1))
    end
    local t = table.remove(AR2_beamPool) or AR2_newTracer()
    if not t then return end

    local col = AR2_tracerColor()
    if col ~= AR2_lastTracerCol then
        AR2_lastTracerCol = col
        AR2_lastTracerSeq = ColorSequence.new(col)
        AR2_tracerColDirty = true
    end

    local bm = t.Beam
    local w = AR2_CFG.misc.tracerWidth or 2
    bm.Color = AR2_lastTracerSeq
    bm.Width0, bm.Width1 = w, w
    bm.LightEmission = math.clamp(AR2_CFG.misc.tracerEmission or 1, 0, 1)
    bm.Brightness = 10000 ^ ((math.clamp(AR2_CFG.misc.tracerGlow or 6, 1, 25) - 1) / 24)
    bm.Transparency = AR2_TRACER_SEQ_CLEAR

    local tex = AR2_TRACER_STYLES[AR2_CFG.misc.tracerStyle] or ""
    bm.Texture = tex
    if tex ~= "" then
        bm.TextureLength = math.max(0.1, AR2_CFG.misc.tracerTexLength or 4)
        bm.TextureSpeed  = AR2_CFG.misc.tracerTexSpeed or 1
    end

    t.Near.Position = a
    t.Start, t.Finish = a, b2
    t.CreatedAt = tick()
    t.Life = math.max(0.1, AR2_CFG.misc.tracerLife or 0.6)
    t.Fade = math.max(0, AR2_CFG.misc.tracerFade or 0.35)
    t.Expand = AR2_CFG.misc.tracerExpand == true
    t.Step = -1
    if t.Expand then
        t.P, t.V = 0, 0
        t.Far.Position = a
    else
        t.P, t.V = 1, 0
        t.Far.Position = b2
    end
    bm.Enabled = true
    AR2_tracers[#AR2_tracers + 1] = t
end

local function AR2_stepTracers(dt)
    if #AR2_tracers == 0 then return end
    local now = tick()
    local recol = nil
    if AR2_tracerColDirty then
        recol, AR2_tracerColDirty = AR2_lastTracerSeq, false
    end
    local s = math.max(1, AR2_CFG.misc.tracerExpandSpeed or 18)
    local d = math.clamp(AR2_CFG.misc.tracerDamper or 0.7, 0.1, 1)
    local sub = math.max(1, math.ceil(dt * s * d / 0.5))
    local h = dt / sub

    for i = #AR2_tracers, 1, -1 do
        local t = AR2_tracers[i]
        local age = now - t.CreatedAt
        local broken = (not t.Beam) or t.Beam.Parent == nil
        if broken or age >= t.Life + t.Fade then
            table.remove(AR2_tracers, i)
            AR2_freeTracer(t, broken)
        else
            if recol then t.Beam.Color = recol end
            if t.Expand then
                for _ = 1, sub do
                    t.V = t.V + (1 - t.P) * s * s * h - t.V * 2 * d * s * h
                    t.P = t.P + t.V * h
                end
                t.Far.Position = t.Start:Lerp(t.Finish, math.clamp(t.P, 0, 1))
                if math.abs(t.P - 1) < 0.002 and math.abs(t.V) < 0.05 then
                    t.Expand = false
                    t.Far.Position = t.Finish
                end
            end
            if t.Fade > 0 and age >= t.Life then
                local step = math.clamp(
                    math.floor(((age - t.Life) / t.Fade) * 16 + 0.5), 0, 16)
                if step ~= t.Step then
                    t.Step = step
                    t.Beam.Transparency = AR2_TRACER_FADE_LUT[step]
                end
            end
        end
    end
end

function AR2_addTracer(origin, direction)
    if not AR2_tracersEnabled then return end

    if AR2_CFG.misc.tracerStyle == "3D Part" then
        local dir = direction.Unit
        local length = (AR2_CFG.misc.tracerLength or 10) * 100
        local col = AR2_CFG.misc.tracerRainbow
            and Color3.fromHSV((tick()*1000%360)/360,1,1)
            or  Color3.fromHSV((AR2_CFG.misc.tracerHue or 0)/360,1,1)
        local w = math.max(0.05,(AR2_CFG.misc.tracerWidth or 2)*0.06)
        local pt = Instance.new("Part")
        pt.Anchored=true pt.CanCollide=false pt.CanQuery=false pt.CanTouch=false pt.CastShadow=false
        pt.Material=Enum.Material.Neon pt.Color=col
        pt.Size=Vector3.new(w,w,length)
        pt.CFrame=CFrame.lookAt(origin, origin+dir)*CFrame.new(0,0,-length/2)
        pt.Parent=AR2_Workspace
        task.spawn(function()
            local steps=14
            for i=1,steps do
                if not pt.Parent then return end
                pcall(function() pt.Transparency=i/steps end)
                task.wait(0.4/steps)
            end
            pcall(function() pt:Destroy() end)
        end)
        return
    end
    local terrain = AR2_Workspace.Terrain
    if not terrain then return end
    local length = (AR2_CFG.misc.tracerLength or 10) * 100
    local dir = direction.Unit

    if AR2_CFG.misc.tracerLightning then
        local perp1 = Vector3.new(-dir.Z, 0, dir.X)
        local perp2 = Vector3.new(0, dir.Z, -dir.X)
        local segments = 20
        local points = {origin}
        for i = 1, segments do
            local frac = i / segments
            local basePoint = origin + dir * (length * frac)
            local offset = (perp1 * (math.random() - 0.5) + perp2 * (math.random() - 0.5)) * (math.random() * 6 + 2)
            table.insert(points, basePoint + offset)
        end
        for i = 1, #points - 1 do
            AR2_makeBeamTracer(points[i], points[i + 1])
        end
        return
    end

    AR2_makeBeamTracer(origin, origin + dir * length)
end

function AR2_tracersStart()
    if AR2_tracersConn then return end
    AR2_tracersConn = AR2_RunService.Heartbeat:Connect(function(dt)
        pcall(AR2_stepTracers, dt)
    end)
end

function AR2_tracersStop()
    if AR2_tracersConn then
        pcall(function() AR2_tracersConn:Disconnect() end)
        AR2_tracersConn = nil
    end
    pcall(AR2_tracersClear)
end

function AR2_tracersUpdate()
    AR2_tracersEnabled = AR2_CFG.misc.tracers
    if AR2_tracersEnabled then
        AR2_tracersStart()
    else
        AR2_tracersStop()
    end
end
end

-- ============================================================
-- 33. SPIN BOT
-- ============================================================
do
    local joint, baseC0, angle = nil, nil, 0
    local function findJoint()
        local ch = AR2_LocalPlayer.Character
        if not ch then return nil end
        local lt = ch:FindFirstChild("LowerTorso")
        if lt then for _, d in ipairs(lt:GetChildren()) do if d:IsA("Motor6D") then return d end end end
        local hrp = ch:FindFirstChild("HumanoidRootPart")
        if hrp then local rj = hrp:FindFirstChild("RootJoint") if rj and rj:IsA("Motor6D") then return rj end end
        return nil
    end
    function AR2_setSpin(on)
        AR2_CFG.misc.spinOn = on and true or false
        if not on then
            if joint and joint.Parent and baseC0 then pcall(function() joint.C0 = baseC0 end) end
            joint, baseC0 = nil, nil
        end
    end
    AR2_LocalPlayer.CharacterAdded:Connect(function() joint, baseC0 = nil, nil end)
    pcall(function()
        AR2_RunService:BindToRenderStep("AR2_SpinBot", Enum.RenderPriority.Character.Value + 1, function(dt)
            if not AR2_CFG.misc.spinOn then return end
            if not joint or not joint.Parent then joint = findJoint() baseC0 = joint and joint.C0 or nil end
            if not joint or not baseC0 then return end
            angle = (angle + (AR2_CFG.misc.spinSpeed or 20) * dt) % (math.pi * 2)
            local cf = CFrame.Angles(0, angle, 0)
            if AR2_CFG.misc.spinTilt then cf = cf * CFrame.Angles(math.rad(25) * math.sin(angle * 2), 0, 0) end
            pcall(function() joint.C0 = baseC0 * cf end)
        end)
    end)
end

-- ============================================================
-- 34. HIDE PLAYER / HIDE GUN
-- ============================================================
AR2_hidePlayerOriginal = {}

function AR2_refreshHideState()
    local char = AR2_LocalPlayer.Character
    if not char then return end
    local shouldHide = AR2_CFG.misc.hidePlayer
                    or (AR2_CFG.misc.morphHideSelf and AR2_morph and AR2_morph.model)
    if shouldHide then
        for _, descendant in ipairs(char:GetDescendants()) do
            if descendant:IsA("BasePart")
            or descendant:IsA("Decal")
            or descendant:IsA("Texture") then
                if AR2_hidePlayerOriginal[descendant] == nil then
                    AR2_hidePlayerOriginal[descendant] = descendant.Transparency
                end
                descendant.Transparency = 1
            end
        end
    else
        for part, orig in pairs(AR2_hidePlayerOriginal) do
            if part and part.Parent then part.Transparency = orig end
        end
        table.clear(AR2_hidePlayerOriginal)
    end
end

function AR2_toggleHidePlayer(enabled)
    AR2_CFG.misc.hidePlayer = enabled
    AR2_refreshHideState()
end

local AR2_hideGunOriginal = {}
local AR2_hideGunConn = nil
function AR2_findGunModel()
    local char = AR2_LocalPlayer.Character
    if not char then return nil end
    local equipped = char:FindFirstChild("Equipped")
    if equipped then
        for _, child in ipairs(equipped:GetChildren()) do
            if child:IsA("Model") or child:IsA("Tool") then return child end
        end
    end
    for _, child in ipairs(char:GetChildren()) do
        if child:IsA("Tool") then return child end
    end
    return nil
end

function AR2_toggleHideGun(enabled)
    AR2_CFG.misc.hideGun = enabled
    if enabled then
        if not AR2_hideGunConn then
            AR2_hideGunConn = AR2_RunService.RenderStepped:Connect(function()
                local gun = AR2_findGunModel()
                if gun then
                    for _, descendant in ipairs(gun:GetDescendants()) do
                        if descendant:IsA("BasePart") then
                            if AR2_hideGunOriginal[descendant] == nil then
                                AR2_hideGunOriginal[descendant] = descendant.Transparency
                                descendant.Transparency = 1
                            end
                        end
                    end
                end
            end)
        end
    else
        if AR2_hideGunConn then AR2_hideGunConn:Disconnect() AR2_hideGunConn = nil end
        for part, orig in pairs(AR2_hideGunOriginal) do
            if part.Parent then part.Transparency = orig end
        end
        table.clear(AR2_hideGunOriginal)
    end
end

-- ============================================================
-- 35. PERSONAL CHAR / GUN
-- ============================================================
local personalCharOriginal    = {}
local personalCharRainbowConn = nil

function AR2_setPersonalCharacter(enabled, mode, hue, gray, materialStr, rainbow)
    AR2_CFG.misc.personalCharOn = enabled
    if mode then AR2_CFG.misc.personalCharMode = mode end
    if hue then AR2_CFG.misc.personalCharHue = hue end
    if gray then AR2_CFG.misc.personalCharGray = gray end
    if materialStr then AR2_CFG.misc.personalCharMaterial = materialStr end
    if rainbow ~= nil then AR2_CFG.misc.personalCharRainbow = rainbow end
    if not enabled then
        if personalCharRainbowConn then personalCharRainbowConn:Disconnect() personalCharRainbowConn = nil end
        for part, orig in pairs(personalCharOriginal) do
            if part.Parent then
                part.Color = orig.Color
                part.Material = orig.Material
                part.Transparency = orig.Transparency
            end
        end
        table.clear(personalCharOriginal)
        return
    end
    local char = AR2_LocalPlayer.Character
    if not char then return end
    task.wait(0.5)
    local material = Enum.Material[AR2_CFG.misc.personalCharMaterial] or Enum.Material.ForceField
    local targetParts = { "Head","UpperTorso","LowerTorso",
        "LeftUpperArm","LeftLowerArm","LeftHand",
        "RightUpperArm","RightLowerArm","RightHand",
        "LeftUpperLeg","LeftLowerLeg","LeftFoot",
        "RightUpperLeg","RightLowerLeg","RightFoot" }
    local function getColor()
        if AR2_CFG.misc.personalCharMode == "BlackWhite" then
            local g = AR2_CFG.misc.personalCharGray / 100
            return Color3.fromRGB(g*255, g*255, g*255)
        else
            return Color3.fromHSV(AR2_CFG.misc.personalCharHue/360, 1, 1)
        end
    end
    for _, name in ipairs(targetParts) do
        local part = char:FindFirstChild(name)
        if part and part:IsA("BasePart") then
            if not personalCharOriginal[part] then
                personalCharOriginal[part] = { Color = part.Color, Material = part.Material, Transparency = part.Transparency }
            end
            part.Material = material
            if material == Enum.Material.ForceField then part.Transparency = 0.5 else part.Transparency = 0 end
            part.Color = getColor()
        end
    end
    if AR2_CFG.misc.personalCharRainbow then
        if personalCharRainbowConn then personalCharRainbowConn:Disconnect() end
        personalCharRainbowConn = AR2_RunService.Heartbeat:Connect(function()
            local hue2 = (tick() * 100) % 360
            local color = Color3.fromHSV(hue2/360, 1, 1)
            for _, name in ipairs(targetParts) do
                local part = char:FindFirstChild(name)
                if part and part:IsA("BasePart") then part.Color = color end
            end
        end)
    else
        if personalCharRainbowConn then personalCharRainbowConn:Disconnect() personalCharRainbowConn = nil end
    end
end

AR2_personalGunOriginal = {}
AR2_personalGunConn = nil

function AR2_findLocalGun()
    local char = AR2_LocalPlayer.Character
    if not char then return nil end
    local equipped = char:FindFirstChild("Equipped")
    if equipped then
        for _, child in ipairs(equipped:GetChildren()) do
            if child:IsA("Model") or child:IsA("Tool") then return child end
        end
    end
    for _, child in ipairs(char:GetChildren()) do
        if child:IsA("Tool") then return child end
    end
    local cam = workspace.CurrentCamera
    if cam then
        for _, child in ipairs(cam:GetChildren()) do
            if child:IsA("Model") and (child.Name:lower():find("gun") or child.Name:lower():find("weapon") or child.Name:lower():find("viewmodel")) then
                return child
            end
        end
    end
    return nil
end

function AR2_setPersonalGun(enabled, mode, hue, gray, material, rainbow)
    AR2_CFG.misc.personalGunOn       = enabled and true or false
    AR2_CFG.misc.personalGunMode     = mode or AR2_CFG.misc.personalGunMode
    AR2_CFG.misc.personalGunHue      = hue or AR2_CFG.misc.personalGunHue
    AR2_CFG.misc.personalGunGray     = gray or AR2_CFG.misc.personalGunGray
    AR2_CFG.misc.personalGunMaterial = material or AR2_CFG.misc.personalGunMaterial
    AR2_CFG.misc.personalGunRainbow  = rainbow and true or false

    if AR2_personalGunConn then AR2_personalGunConn:Disconnect() AR2_personalGunConn = nil end

    local function restore()
        for part, orig in pairs(AR2_personalGunOriginal) do
            if part and part.Parent then
                pcall(function()
                    part.Color        = orig.Color
                    part.Material     = orig.Material
                    part.Transparency = orig.Transparency
                end)
            end
        end
        AR2_personalGunOriginal = {}
    end

    if not enabled then restore() return end

    local function applyNow()
        local gun = AR2_findLocalGun()
        if not gun then return end

        local col
        if AR2_CFG.misc.personalGunRainbow then
            AR2_CFG.misc.personalGunHue = (AR2_CFG.misc.personalGunHue + 3) % 360
            col = Color3.fromHSV(AR2_CFG.misc.personalGunHue / 360, 1, 1)
        elseif AR2_CFG.misc.personalGunMode == "BlackWhite" then
            local g = (AR2_CFG.misc.personalGunGray or 100) / 100
            col = Color3.fromRGB(g * 255, g * 255, g * 255)
        else
            col = Color3.fromHSV((AR2_CFG.misc.personalGunHue or 0) / 360, 1, 1)
        end

        local mat = Enum.Material[AR2_CFG.misc.personalGunMaterial] or Enum.Material.Neon

        for _, part in ipairs(gun:GetDescendants()) do
            if part:IsA("BasePart") then
                if not AR2_personalGunOriginal[part] then
                    AR2_personalGunOriginal[part] = {
                        Color        = part.Color,
                        Material     = part.Material,
                        Transparency = part.Transparency,
                    }
                end
                part.Color    = col
                part.Material = mat
            end
        end
    end

    applyNow()
    AR2_personalGunConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_CFG.misc.personalGunOn then return end
        applyNow()
    end)
end

-- ============================================================
-- 36. HITMARKER
-- ============================================================
do
    local _host = (typeof(AR2_gethui)=="function" and AR2_gethui()) or game:GetService("CoreGui")
    pcall(function() local o=_host:FindFirstChild("tetescr_hm") if o then o:Destroy() end end)

    local _gui = Instance.new("ScreenGui")
    _gui.Name="tetescr_hm" _gui.ResetOnSpawn=false _gui.IgnoreGuiInset=true
    _gui.DisplayOrder=999999 _gui.Parent=_host

    local function _mkF(rot)
        local f=Instance.new("Frame")
        f.BorderSizePixel=0 f.AnchorPoint=Vector2.new(0.5,0.5)
        f.Rotation=rot f.ZIndex=10 f.Visible=false f.Parent=_gui
        return f
    end
    local function _round(f)
        Instance.new("UICorner",f).CornerRadius=UDim.new(1,0)
    end

    local _xA  = _mkF(45)   local _xB  = _mkF(-45)
    local _pA  = _mkF(0)    local _pB  = _mkF(90)
    local _dot = _mkF(0)    _round(_dot)
    local _dia = _mkF(45)
    local _cT  = _mkF(0)    _round(_cT)
    local _cB  = _mkF(0)    _round(_cB)
    local _cL  = _mkF(0)    _round(_cL)
    local _cR  = _mkF(0)    _round(_cR)
    local _sT  = _mkF(0)    local _sB  = _mkF(0)
    local _sL  = _mkF(90)   local _sR  = _mkF(90)

    local _allFrames = {_xA,_xB,_pA,_pB,_dot,_dia,_cT,_cB,_cL,_cR,_sT,_sB,_sL,_sR}

    local function _hideAll()
        for _,f in ipairs(_allFrames) do f.Visible=false end
    end

    local _hmActive  = false
    local _hmEndTime = 0
    local _rainbowH  = 0

    AR2_HM_SHAPE_NAMES = {"X","+","Dot","Diamond","Circle","Square"}

    local function _applyShape(cx,cy)
        local cfg = AR2_CFG.hitmarker
        local s   = cfg.size      or 20
        local tt  = cfg.thickness or 3
        local g   = cfg.gap       or 6
        local op  = cfg.opacity   or 1
        local tr  = 1 - math.clamp(op,0,1)
        local hue = (cfg.rainbow and _rainbowH or (cfg.colorHue or 0)) / 360
        local col = Color3.fromHSV(hue,1,1)
        local shape = cfg.shape or "X"

        _hideAll()

        local function show(f,x,y,w,h)
            f.Position=UDim2.fromOffset(x,y) f.Size=UDim2.fromOffset(w,h)
            f.BackgroundColor3=col f.BackgroundTransparency=tr f.Visible=true
        end

        if shape=="X" then
            show(_xA,cx,cy,s*2,tt) show(_xB,cx,cy,s*2,tt)
        elseif shape=="+" then
            show(_pA,cx,cy,s*2,tt) show(_pB,cx,cy,tt,s*2)
        elseif shape=="Dot" then
            local r=math.max(tt*2,5) show(_dot,cx,cy,r,r)
        elseif shape=="Diamond" then
            show(_dia,cx,cy,s*1.4,tt)
        elseif shape=="Circle" then
            local r=s+g local ds=math.max(tt+2,6)
            show(_cT,cx,cy-r,ds,ds) show(_cB,cx,cy+r,ds,ds)
            show(_cL,cx-r,cy,ds,ds) show(_cR,cx+r,cy,ds,ds)
        elseif shape=="Square" then
            show(_sT,cx,cy-(s+g),s*2,tt) show(_sB,cx,cy+(s+g),s*2,tt)
            show(_sL,cx-(s+g),cy,tt,s*2) show(_sR,cx+(s+g),cy,tt,s*2)
        end
    end

    AR2_hitmarkerFlag = false

    local function _showVisual()
        local cam=workspace.CurrentCamera if not cam then return end
        if AR2_CFG.hitmarker.rainbow then _rainbowH=(_rainbowH+15)%360 end
        _applyShape(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)
        _hmActive=true _hmEndTime=tick()+(AR2_CFG.hitmarker.duration or 0.3)
    end

    AR2_showHitmarker = function()
        _showVisual()
        local id   = AR2_CFG.hitmarker.sound
        local path = AR2_CFG.hitmarker.soundPath
        local want = (path and path ~= "") and path or id
        if want ~= AR2_hitSoundId then
            AR2_hitSoundLoad(want)
        end
    end

    function AR2_toggleHitmarker(enabled)
        AR2_CFG.hitmarker.enabled=enabled
        if not enabled then AR2_hitmarkerFlag=false _hideAll() _hmActive=false end
    end

    AR2_RunService.RenderStepped:Connect(function()
        if AR2_hitmarkerFlag then AR2_hitmarkerFlag=false AR2_showHitmarker() end
        if _hmActive and tick()>_hmEndTime then _hideAll() _hmActive=false end
    end)
end

-- ============================================================
-- 37. CROSSHAIR
-- ============================================================
local AR2_crosshairDrawings = {}
local AR2_crosshairConn = nil
local function AR2_destroyCrosshair()
    for _,d in ipairs(AR2_crosshairDrawings) do pcall(function() d:Remove() end) end
    AR2_crosshairDrawings = {}
end

function AR2_buildCrosshair()
    AR2_destroyCrosshair()
    if not AR2_CFG.crosshair.enabled or not AR2_hasDrawing then return end
    local cfg = AR2_CFG.crosshair
    local color = Color3.fromHSV(cfg.hue/360,1,1)
    local s,g,tc = cfg.size,cfg.gap,cfg.thickness
    if cfg.shape == "Cross" then
        for i=1,4 do
            local l=Drawing.new("Line")
            l.Thickness=tc l.Color=color l.Transparency=1 l.Visible=true
            AR2_crosshairDrawings[i]=l
        end
    elseif cfg.shape == "Dot" then
        local c=Drawing.new("Circle")
        c.Radius=tc+1 c.Color=color c.Filled=true c.Transparency=1 c.Visible=true
        AR2_crosshairDrawings[1]=c
    elseif cfg.shape == "Circle" then
        local c=Drawing.new("Circle")
        c.Radius=s c.Color=color c.Filled=false c.Thickness=tc c.Transparency=1 c.Visible=true
        AR2_crosshairDrawings[1]=c
    elseif cfg.shape == "T" then
        for i=1,3 do
            local l=Drawing.new("Line")
            l.Thickness=tc l.Color=color l.Transparency=1 l.Visible=true
            AR2_crosshairDrawings[i]=l
        end
    elseif cfg.shape == "Square" then
        local sq=Drawing.new("Square")
        sq.Size=Vector2.new(s*2,s*2) sq.Color=color sq.Filled=false sq.Thickness=tc sq.Transparency=1 sq.Visible=true
        AR2_crosshairDrawings[1]=sq
    end
end

local function AR2_updateCrosshairPositions(center)
    local cfg = AR2_CFG.crosshair
    local color = cfg.rainbow and Color3.fromHSV((tick()*80%360)/360,1,1) or Color3.fromHSV(cfg.hue/360,1,1)
    local s,g,tc = cfg.size,cfg.gap,cfg.thickness
    if cfg.shape=="Cross" and #AR2_crosshairDrawings==4 then
        AR2_crosshairDrawings[1].From=center+Vector2.new(0,-(g+s))
        AR2_crosshairDrawings[1].To=center+Vector2.new(0,-g)
        AR2_crosshairDrawings[2].From=center+Vector2.new(0,g)
        AR2_crosshairDrawings[2].To=center+Vector2.new(0,g+s)
        AR2_crosshairDrawings[3].From=center+Vector2.new(-(g+s),0)
        AR2_crosshairDrawings[3].To=center+Vector2.new(-g,0)
        AR2_crosshairDrawings[4].From=center+Vector2.new(g,0)
        AR2_crosshairDrawings[4].To=center+Vector2.new(g+s,0)
        for _,d in ipairs(AR2_crosshairDrawings) do d.Color=color d.Thickness=tc end
    elseif cfg.shape=="Dot" and #AR2_crosshairDrawings==1 then
        AR2_crosshairDrawings[1].Position=center
        AR2_crosshairDrawings[1].Radius=tc+1
        AR2_crosshairDrawings[1].Color=color
    elseif cfg.shape=="Circle" and #AR2_crosshairDrawings==1 then
        AR2_crosshairDrawings[1].Position=center
        AR2_crosshairDrawings[1].Radius=s
        AR2_crosshairDrawings[1].Color=color
        AR2_crosshairDrawings[1].Thickness=tc
    elseif cfg.shape=="T" and #AR2_crosshairDrawings==3 then
        AR2_crosshairDrawings[1].From=center+Vector2.new(0,-(g+s))
        AR2_crosshairDrawings[1].To=center+Vector2.new(0,-g)
        AR2_crosshairDrawings[2].From=center+Vector2.new(-(g+s),0)
        AR2_crosshairDrawings[2].To=center+Vector2.new(-g,0)
        AR2_crosshairDrawings[3].From=center+Vector2.new(g,0)
        AR2_crosshairDrawings[3].To=center+Vector2.new(g+s,0)
        for _,d in ipairs(AR2_crosshairDrawings) do d.Color=color d.Thickness=tc end
    elseif cfg.shape=="Square" and #AR2_crosshairDrawings==1 then
        AR2_crosshairDrawings[1].Position=center-Vector2.new(s,s)
        AR2_crosshairDrawings[1].Size=Vector2.new(s*2,s*2)
        AR2_crosshairDrawings[1].Color=color
        AR2_crosshairDrawings[1].Thickness=tc
    end
end

function AR2_toggleCrosshair(enabled)
    AR2_CFG.crosshair.enabled = enabled
    if AR2_crosshairConn then AR2_crosshairConn:Disconnect() AR2_crosshairConn=nil end
    AR2_destroyCrosshair()
    if not enabled then return end
    AR2_buildCrosshair()
    AR2_crosshairConn = AR2_RunService.RenderStepped:Connect(function()
        if not AR2_CFG.crosshair.enabled then return end
        local cam = AR2_Camera if not cam then return end
        local expected = ({Cross=4,Dot=1,Circle=1,T=3,Square=1})[AR2_CFG.crosshair.shape] or 1
        if #AR2_crosshairDrawings ~= expected then AR2_buildCrosshair() end
        local center = Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)
        AR2_updateCrosshairPositions(center)
    end)
end

-- ============================================================
-- 38. TP
-- ============================================================
AR2_tp = AR2_tp or {}
do
    local _tpDefaults = { busy = false, cancel = false, gen = 0, lastResult = "", writes = 0, phase = "idle", attempt = 0, pulls = 0, hops = 0, surfaceTarget = nil, under = false, underGoal = nil, clipSaved = nil, clipAt = 0 }
    for k, v in pairs(_tpDefaults) do AR2_tp[k] = v end
end
AR2_tpGuard = { installed = false, active = false, blocked = 0, why = "", goal = nil }
AR2_hookmetamethod = hookmetamethod
AR2_checkcaller = checkcaller
AR2_getnamecallmethod = getnamecallmethod
AR2_newcclosure = newcclosure or function(fn) return fn end
AR2_GUARD_PROPS = { CFrame = true, Position = true, WorldCFrame = true }
AR2_GUARD_METHODS = { PivotTo = true, SetPrimaryPartCFrame = true, MoveTo = true, ApplyImpulse = true }

function AR2_tpGuard.install()
    if AR2_tpGuard.installed then return true end
    if not (AR2_hookmetamethod and AR2_checkcaller) then AR2_tpGuard.why = "no hookmetamethod/checkcaller" AR2_logHook("TPGuard", false, AR2_tpGuard.why) return false, AR2_tpGuard.why end
    local okIndex = pcall(function()
        local old
        old = AR2_hookmetamethod(game, "__newindex", AR2_newcclosure(function(self, key, value)
            if AR2_tpGuard.active and AR2_GUARD_PROPS[key] and not AR2_checkcaller() then
                if self == AR2_tpRoot() then
                    local position = (typeof(value) == "CFrame" and value.Position) or (typeof(value) == "Vector3" and value) or nil
                    if AR2_tpGuard.goal and position and (position - AR2_tpGuard.goal).Magnitude >= (AR2_CFG.tp.snapBack or 8) then
                        AR2_tpGuard.blocked = AR2_tpGuard.blocked + 1
                        return
                    end
                end
            end
            return old(self, key, value)
        end))
    end)
    local okCall = pcall(function()
        local old
        old = AR2_hookmetamethod(game, "__namecall", AR2_newcclosure(function(self, ...)
            if AR2_tpGuard.active and not AR2_checkcaller() then
                local method = AR2_getnamecallmethod and AR2_getnamecallmethod() or ""
                if AR2_GUARD_METHODS[method] and (self == AR2_tpRoot() or self == AR2_LocalPlayer.Character) then
                    AR2_tpGuard.blocked = AR2_tpGuard.blocked + 1
                    return
                end
            end
            return old(self, ...)
        end))
    end)
    if not (okIndex or okCall) then AR2_tpGuard.why = "both hooks failed" return false, AR2_tpGuard.why end
    AR2_tpGuard.installed = true
    AR2_tpGuard.why = "armed"
    AR2_logHook("TPGuard", true)
    return true
end

function AR2_tpRoot()
    local char = AR2_LocalPlayer.Character
    return char and char:FindFirstChild("HumanoidRootPart")
end

function AR2_tpUnseat()
    if not AR2_CFG.tp.unseat then return end
    local char = AR2_LocalPlayer.Character
    if not char then return end
    local humanoid = char:FindFirstChildWhichIsA("Humanoid")
    if humanoid and humanoid.Sit then
        humanoid.Sit = false
        pcall(function() humanoid:ChangeState(Enum.HumanoidStateType.GettingUp) end)
    end
    for _, part in ipairs(char:GetDescendants()) do
        if part:IsA("Weld") and part.Name == "SeatWeld" then part:Destroy() end
    end
end

function AR2_tpWrite(root, goal)
    AR2_tpGuard.goal = goal.Position
    root.AssemblyLinearVelocity = Vector3.zero
    root.AssemblyAngularVelocity = Vector3.zero
    if AR2_CFG.tp.force then
        root.Anchored = true
        local char = AR2_LocalPlayer.Character
        local humanoid = char and char:FindFirstChildWhichIsA("Humanoid")
        if humanoid then
            humanoid.PlatformStand = true
            humanoid.AutoRotate = false
        end
    end
    root.CFrame = goal
end

function AR2_tpRelease()
    local root = AR2_tpRoot()
    if root then
        root.Anchored = false
        root.AssemblyLinearVelocity = Vector3.zero
    end
    for part in pairs(AR2_tp.clipSaved or {}) do
        pcall(function() if part.Parent then part.CanCollide = true end end)
    end
    AR2_tp.clipSaved = nil
    local char = AR2_LocalPlayer.Character
    local humanoid = char and char:FindFirstChildWhichIsA("Humanoid")
    if humanoid then
        humanoid.PlatformStand = false
        humanoid.AutoRotate = true
    end
end

function AR2_tpYield()
    if AR2_CFG.tp.everyFrame then AR2_RunService.Heartbeat:Wait() else task.wait(AR2_CFG.tp.intervalCs/100) end
end

function AR2_tpAlive(mine) return AR2_tp.gen == mine and not AR2_tp.cancel end

function AR2_tpGroundStand(position)
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = { AR2_LocalPlayer.Character }
    params.IgnoreWater = true
    local hit = AR2_Workspace:Raycast(position + Vector3.new(0,3,0), Vector3.new(0,-12,0), params)
    if not hit then return position end
    return Vector3.new(position.X, hit.Position.Y + 3, position.Z)
end

function AR2_tpGroundBelow(position)
    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = { AR2_LocalPlayer.Character }
    params.IgnoreWater = true
    local hit = AR2_Workspace:Raycast(position + Vector3.new(0,2,0), Vector3.new(0,-4000,0), params)
    if hit then return math.min(hit.Position.Y, position.Y) end
    return position.Y - 20
end

function AR2_tpNoclip(on)
    local char = AR2_LocalPlayer.Character
    if not char then return end
    if on then
        AR2_tp.clipSaved = AR2_tp.clipSaved or {}
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") and part.CanCollide then
                AR2_tp.clipSaved[part] = true
                part.CanCollide = false
            end
        end
    else
        for part in pairs(AR2_tp.clipSaved or {}) do
            pcall(function() if part.Parent then part.CanCollide = true end end)
        end
        AR2_tp.clipSaved = nil
    end
end

function AR2_tpPush(goalFn, mine, seconds)
    local c = AR2_CFG.tp
    local started = os.clock()
    local stableSince, lastGoal = nil, nil
    local needed = math.min(c.stableCs/100, seconds*0.6)
    while AR2_tpAlive(mine) do
        local root = AR2_tpRoot()
        local goal = root and goalFn() or nil
        if not root or not goal then return false end
        if lastGoal then
            if (root.Position - lastGoal).Magnitude >= c.snapBack then
                stableSince = nil
            else
                stableSince = stableSince or os.clock()
            end
        end
        AR2_tpWrite(root, goal)
        lastGoal = goal.Position
        AR2_tp.writes = AR2_tp.writes + 1
        if stableSince and os.clock() - stableSince >= needed then return true end
        if os.clock() - started >= seconds then return false end
        AR2_tpYield()
    end
    return false
end

function AR2_tpHop(goalFn, mine)
    local c = AR2_CFG.tp
    local hop = math.max(4, c.hopStuds)
    for _ = 1, 600 do
        if not AR2_tpAlive(mine) then return false end
        local root = AR2_tpRoot()
        local goal = root and goalFn() or nil
        if not root or not goal then return false end
        local delta = goal.Position - root.Position
        if delta.Magnitude <= hop then return AR2_tpPush(goalFn, mine, c.maxSeconds) end
        local before = root.Position
        local heading = delta.Unit
        local waypoint = CFrame.new(before + heading * hop)
        AR2_tp.phase = string.format("hopping %.0f studs left", delta.Magnitude)
        local ok = AR2_tpPush(function() return waypoint end, mine, c.stableCs/100 + 1.5)
        AR2_RunService.Heartbeat:Wait()
        AR2_RunService.Heartbeat:Wait()
        local after = AR2_tpRoot()
        if not after then return false end
        local gained = (after.Position - before):Dot(heading)
        if ok and gained >= hop*0.5 then
            AR2_tp.hops = AR2_tp.hops + 1
        else
            hop = hop/2
            if hop < 4 then return false end
        end
    end
    return false
end

function AR2_tpHold(anchorPoint, mine)
    local c = AR2_CFG.tp
    if c.holdSecs <= 0 then return true end
    local until_ = os.clock() + c.holdSecs
    local last = anchorPoint.Position
    AR2_tp.phase = "holding"
    while os.clock() < until_ and AR2_tpAlive(mine) do
        local dt = AR2_RunService.Heartbeat:Wait()
        local root = AR2_tpRoot()
        if root then
            local jump = (root.Position - last).Magnitude
            last = root.Position
            if jump / math.max(dt, 1/240) >= 60 then
                AR2_tp.pulls = AR2_tp.pulls + 1
                if not AR2_tpPush(function() return anchorPoint end, mine, c.maxSeconds) then return false end
                last = anchorPoint.Position
            end
        end
    end
    return true
end

function AR2_tpUnderground(goalFn, mine)
    local c = AR2_CFG.tp
    local root = AR2_tpRoot()
    local goal = root and goalFn() or nil
    if not root or not goal then return false end
    local depth = math.max(5, c.burrowDepth)
    AR2_tp.phase = "burrowing"
    local under = Vector3.new(root.Position.X, AR2_tpGroundBelow(root.Position) - depth, root.Position.Z)
    if c.burrowClip then AR2_tpNoclip(true) end
    local landed = false
    if AR2_tpPush(function() return CFrame.new(under) end, mine, c.maxSeconds) then
        AR2_tp.phase = "crossing under"
        local destination = goalFn()
        if destination then
            local floor = AR2_tpGroundBelow(destination.Position)
            local cruise = math.min(under.Y, floor - depth)
            local across = CFrame.new(destination.Position.X, cruise, destination.Position.Z)
            if AR2_tpPush(function() return across end, mine, c.maxSeconds) then
                AR2_tp.hops = AR2_tp.hops + 1
            end
        end
        AR2_tp.phase = "surfacing"
        landed = AR2_tpPush(goalFn, mine, c.maxSeconds)
        if c.burrowClip then AR2_tpNoclip(false) end
    end
    return landed
end

function AR2_tpTargetRoot()
    local wanted = AR2_CFG.tp.target
    if not wanted or wanted == "-" then return nil end
    for _, entry in ipairs(AR2_api.players()) do
        if entry.name == wanted or entry.displayName == wanted then return entry.root end
    end
    return nil
end

function AR2_tpOffset(cframe)
    local c = AR2_CFG.tp
    return cframe * CFrame.new(c.side, c.above, c.behind)
end

function AR2_tp.run(getGoal, label, keepGoing)
    AR2_tp.gen = (AR2_tp.gen or 0) + 1
    local mine = AR2_tp.gen
    AR2_tp.cancel = true
    task.spawn(function()
        while AR2_tp.busy do task.wait() end
        if AR2_tp.gen ~= mine then return end
        local c = AR2_CFG.tp
        AR2_tp.busy, AR2_tp.cancel = true, false
        AR2_tp.writes, AR2_tp.attempt, AR2_tp.pulls, AR2_tp.hops = 0, 0, 0, 0
        local started = os.clock()
        AR2_tpUnseat()
        if c.guard then
            AR2_tpGuard.install()
            AR2_tpGuard.blocked = 0
            AR2_tpGuard.active = true
        end
        if c.force and not keepGoing then
            AR2_tp.phase = "FORCED"
            local until_ = os.clock() + c.forceSecs
            while os.clock() < until_ and AR2_tpAlive(mine) do
                local root = AR2_tpRoot()
                local goal = root and getGoal() or nil
                if not root or not goal then break end
                AR2_tpWrite(root, goal)
                AR2_tp.writes = AR2_tp.writes + 1
                AR2_RunService.Heartbeat:Wait()
            end
            AR2_tpRelease()
            local landing = AR2_tpRoot()
            local goal = getGoal()
            AR2_RunService.Heartbeat:Wait()
            AR2_RunService.Heartbeat:Wait()
            local drift = (landing and goal) and (landing.Position - goal.Position).Magnitude or -1
            AR2_tp.lastResult = string.format("%s -- forced %d writes, %.0f off%s", label, AR2_tp.writes, drift, drift > c.snapBack and "  <-- server did NOT accept it" or "")
        elseif keepGoing then
            AR2_tp.phase = "following"
            while AR2_tpAlive(mine) do
                local root = AR2_tpRoot()
                local goal = root and getGoal() or nil
                if root and goal then
                    AR2_tpWrite(root, goal)
                    AR2_tp.writes = AR2_tp.writes + 1
                end
                AR2_tpYield()
            end
            AR2_tp.lastResult = string.format("%s -- followed, %d writes", label, AR2_tp.writes)
        else
            local landed = false
            repeat
                if c.underground and AR2_tpAlive(mine) then landed = AR2_tpUnderground(getGoal, mine) end
                for attempt = 1, math.max(1, c.retries) do
                    if landed or not AR2_tpAlive(mine) then break end
                    AR2_tp.attempt = AR2_tp.attempt + 1
                    AR2_tp.phase = string.format("attempt %d", AR2_tp.attempt)
                    landed = AR2_tpPush(getGoal, mine, c.maxSeconds)
                    if landed then break end
                end
                if not landed and c.chunked and AR2_tpAlive(mine) then
                    landed = AR2_tpHop(getGoal, mine)
                end
                if not landed then AR2_RunService.Heartbeat:Wait() end
            until landed or not AR2_tpAlive(mine) or not c.neverGiveUp
            if landed then
                local resting = AR2_tpRoot()
                local anchorPoint = resting and CFrame.new(resting.Position) or nil
                AR2_tp.lastResult = string.format("%s -- landed in %.1fs (%d writes, %d attempts%s)", label, os.clock()-started, AR2_tp.writes, AR2_tp.attempt, AR2_tp.hops > 0 and (", "..AR2_tp.hops.." hops") or "")
                local held = true
                if anchorPoint then held = AR2_tpHold(anchorPoint, mine) end
                if not held then
                    AR2_tp.lastResult = string.format("%s -- landed, then pulled back %d time(s)", label, AR2_tp.pulls)
                elseif AR2_tp.pulls > 0 then
                    AR2_tp.lastResult = AR2_tp.lastResult .. string.format(" -- pulled back %d time(s)", AR2_tp.pulls)
                end
            else
                AR2_tp.lastResult = string.format("%s -- REFUSED after %d attempts and %d writes", label, AR2_tp.attempt, AR2_tp.writes)
            end
        end
        AR2_tpRelease()
        if c.guard then
            task.delay(math.max(0, c.guardSecs), function()
                if not AR2_tp.busy then AR2_tpGuard.active = false end
            end)
        end
        if AR2_tp.cancel and not string.find(AR2_tp.lastResult, "landed", 1, true) then
            AR2_tp.lastResult = label .. " -- stopped"
        end
        AR2_tp.busy, AR2_tp.phase = false, "idle"
    end)
end

function AR2_tp.stop()
    AR2_tp.cancel = true
    AR2_tp.gen = (AR2_tp.gen or 0) + 1
end

function AR2_tp.toPosition(position, label)
    AR2_tp.run(function() return CFrame.new(position) end, label)
end

function AR2_tp.toPlayer()
    local wanted = AR2_CFG.tp.target
    if wanted == "-" then AR2_tp.lastResult = "no player picked" return end
    AR2_tp.run(function()
        local root = AR2_tpTargetRoot()
        return root and AR2_tpOffset(root.CFrame) or nil
    end, wanted, false)
end

function AR2_tp.saveWaypoint()
    local root = AR2_tpRoot()
    if not root then return false end
    local list = AR2_CFG.tp.waypoints
    local position = root.Position
    list[#list+1] = { name = string.format("WP %d", #list+1), x = math.floor(position.X+0.5), y = math.floor(position.Y+0.5), z = math.floor(position.Z+0.5) }
    return true
end

AR2_respawnTP = false
function AR2_toggleRespawnTP(enabled)
    AR2_respawnTP = enabled
    AR2_CFG.tp.respawnTP = enabled
    if enabled then
        task.spawn(function()
            while AR2_respawnTP do
                AR2_tp.toPlayer()
                task.wait(1)
            end
        end)
    end
end

-- ============================================================
-- 39. NOCLIP
-- ============================================================
AR2_noclipConn = nil
AR2_noclipKeys = {W=false,A=false,S=false,D=false,Space=false,LeftControl=false}
local noclipParts = {}
local noclipOffsets = {}
local noclipRootPart = nil
local isSitting = false

function AR2_startNoclip()
    if AR2_noclipConn then return end
    local char = AR2_LocalPlayer.Character
    if not char then return end
    local hum = char:FindFirstChild("Humanoid")
    if not hum then return end
    hum.Sit = true
    hum.PlatformStand = true
    isSitting = true
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local parts = {}
    local radius = 15
    for _, obj in ipairs(AR2_Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and (obj.Position - root.Position).Magnitude <= radius then
            table.insert(parts, obj)
        end
    end
    if #parts == 0 then
        AR2_CFG.misc.noclip = false
        return
    end

    noclipOffsets = {}
    for _, part in ipairs(parts) do
        noclipOffsets[part] = root.CFrame:Inverse() * part.CFrame
    end
    noclipRootPart = root
    noclipParts = parts

    for _, part in ipairs(parts) do part.Anchored = true end

    AR2_noclipConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_CFG.misc.noclip then AR2_stopNoclip() return end
        if not noclipRootPart or not noclipRootPart.Parent then AR2_stopNoclip() return end

        local hum2 = AR2_LocalPlayer.Character and AR2_LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum2 then
            hum2.Sit = true
            hum2.PlatformStand = true
            isSitting = true
        else
            AR2_stopNoclip()
            return
        end

        local cam = Workspace.CurrentCamera
        local move = Vector3.zero
        if AR2_noclipKeys.W then move += cam.CFrame.LookVector end
        if AR2_noclipKeys.S then move -= cam.CFrame.LookVector end
        if AR2_noclipKeys.D then move += cam.CFrame.RightVector end
        if AR2_noclipKeys.A then move -= cam.CFrame.RightVector end
        if AR2_noclipKeys.Space then move += Vector3.new(0,1,0) end
        if AR2_noclipKeys.LeftControl then move -= Vector3.new(0,1,0) end

        if move.Magnitude > 0 then
            local delta = move.Unit * (AR2_CFG.misc.noclipSpeed or 90) * 0.05
            local newCF = noclipRootPart.CFrame + delta
            noclipRootPart.CFrame = newCF
            for part, offset in pairs(noclipOffsets) do
                if part and part.Parent then part.CFrame = newCF * offset end
            end
        end
    end)
end

function AR2_stopNoclip()
    AR2_CFG.misc.noclip = false
    if AR2_noclipConn then AR2_noclipConn:Disconnect() AR2_noclipConn = nil end
    for _, part in ipairs(noclipParts) do
        if part and part.Parent then
            part.Anchored = false
            part.Velocity = Vector3.zero
            part.RotVelocity = Vector3.zero
        end
    end
    noclipParts = {}
    noclipOffsets = {}
    noclipRootPart = nil
    for k in pairs(AR2_noclipKeys) do AR2_noclipKeys[k] = false end
    local char = AR2_LocalPlayer.Character
    if char then
        local hum = char:FindFirstChild("Humanoid")
        if hum then
            hum.Sit = false
            hum.PlatformStand = false
            isSitting = false
        end
    end
end

function AR2_toggleNoclip(enabled)
    if enabled and not AR2_noclipConn then
        AR2_CFG.misc.noclip = true
        AR2_startNoclip()
    elseif not enabled and AR2_noclipConn then
        AR2_stopNoclip()
    end
end

AR2_UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    local k = input.KeyCode
    if k == Enum.KeyCode.W then AR2_noclipKeys.W = true
    elseif k == Enum.KeyCode.A then AR2_noclipKeys.A = true
    elseif k == Enum.KeyCode.S then AR2_noclipKeys.S = true
    elseif k == Enum.KeyCode.D then AR2_noclipKeys.D = true
    elseif k == Enum.KeyCode.Space then AR2_noclipKeys.Space = true
    elseif k == Enum.KeyCode.LeftControl then AR2_noclipKeys.LeftControl = true end
end)
AR2_UserInputService.InputEnded:Connect(function(input, gpe)
    if gpe then return end
    local k = input.KeyCode
    if k == Enum.KeyCode.W then AR2_noclipKeys.W = false
    elseif k == Enum.KeyCode.A then AR2_noclipKeys.A = false
    elseif k == Enum.KeyCode.S then AR2_noclipKeys.S = false
    elseif k == Enum.KeyCode.D then AR2_noclipKeys.D = false
    elseif k == Enum.KeyCode.Space then AR2_noclipKeys.Space = false
    elseif k == Enum.KeyCode.LeftControl then AR2_noclipKeys.LeftControl = false end
end)

-- ============================================================
-- 40. INF JUMP / BHOP / TP DASH / CAR DASH
-- ============================================================
AR2_infJumpConn = nil
function AR2_startInfJump()
    if AR2_infJumpConn then return end
    AR2_infJumpConn = AR2_UserInputService.InputBegan:Connect(function(input, gpe)
        if not AR2_CFG.misc.infJump then return end
        if gpe then return end
        if input.KeyCode ~= Enum.KeyCode.Space then return end

        local char = AR2_LocalPlayer.Character
        local hrp  = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end

        task.spawn(function()
            if not hrp.Parent then return end
            hrp.Anchored = true
            hrp.CFrame = hrp.CFrame + Vector3.new(0, 15, 0)
            task.wait(0.03)
            if hrp and hrp.Parent then hrp.Anchored = false end
        end)
    end)
end

function AR2_stopInfJump()
    if AR2_infJumpConn then AR2_infJumpConn:Disconnect() AR2_infJumpConn = nil end
end

AR2_bhopConn = nil
function AR2_startBhop()
    if AR2_bhopConn then return end
    AR2_bhopConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_CFG.misc.bhop then AR2_stopBhop() return end
        local char = AR2_LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        local hum  = char and char:FindFirstChildWhichIsA("Humanoid")
        if not root or not hum then return end
        local moving = AR2_UserInputService:IsKeyDown(Enum.KeyCode.W)
                    or AR2_UserInputService:IsKeyDown(Enum.KeyCode.A)
                    or AR2_UserInputService:IsKeyDown(Enum.KeyCode.S)
                    or AR2_UserInputService:IsKeyDown(Enum.KeyCode.D)
        if moving and hum.FloorMaterial ~= Enum.Material.Air then
            root.AssemblyLinearVelocity = Vector3.new(
                root.AssemblyLinearVelocity.X,
                AR2_CFG.misc.bhopHeight * 3,
                root.AssemblyLinearVelocity.Z
            )
        end
    end)
end

function AR2_stopBhop()
    AR2_CFG.misc.bhop = false
    if AR2_bhopConn then AR2_bhopConn:Disconnect() AR2_bhopConn = nil end
end

AR2_tpDashConn = nil
AR2_tpDashLast = 0

function AR2_startTpDash()
    if AR2_tpDashConn then return end
    AR2_CFG.misc.tpDash = true
    AR2_tpDashLast = os.clock()
    AR2_tpDashConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_CFG.misc.tpDash then AR2_stopTpDash() return end
        local now = os.clock()
        if now - AR2_tpDashLast < AR2_CFG.misc.tpDashInterval then return end
        AR2_tpDashLast = now
        local char = AR2_LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local fwd  = workspace.CurrentCamera.CFrame.LookVector
        local flat = Vector3.new(fwd.X, 0, fwd.Z)
        if flat.Magnitude < 0.01 then return end
        local verticalOffset = AR2_CFG.misc.tpDashVert or 0
        local movement = flat.Unit * AR2_CFG.misc.tpDashStuds + Vector3.new(0, verticalOffset, 0)
        root.CFrame = root.CFrame + movement
    end)
end

function AR2_stopTpDash()
    AR2_CFG.misc.tpDash = false
    if AR2_tpDashConn then AR2_tpDashConn:Disconnect() AR2_tpDashConn = nil end
end

AR2_carDashConn = nil
AR2_carDashLast = 0

function AR2_startCarDash()
    if AR2_carDashConn then return end
    AR2_CFG.car.tpDash = true
    AR2_carDashLast = os.clock()

    AR2_carDashConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_CFG.car.tpDash then AR2_stopCarDash() return end
        local now = os.clock()
        if now - AR2_carDashLast < AR2_CFG.car.tpDashInterval then return end
        AR2_carDashLast = now
        local model = AR2_carPick()
        if not model then return end
        local fwd  = workspace.CurrentCamera.CFrame.LookVector
        local flat = Vector3.new(fwd.X, 0, fwd.Z)
        if flat.Magnitude < 0.01 then return end
        flat = flat.Unit
        local verticalOffset = AR2_CFG.car.tpDashVert or 0
        local movement = flat * AR2_CFG.car.tpDashStuds + Vector3.new(0, verticalOffset, 0)
        local curPivot = model:GetPivot()
        local newPivot = CFrame.new(curPivot.Position + movement) * (curPivot - curPivot.Position)
        local savedAnchor = {}
        for _, p in ipairs(model:GetDescendants()) do
            if p:IsA("BasePart") then
                savedAnchor[p] = p.Anchored
                p.Anchored = true
                p.AssemblyLinearVelocity = Vector3.zero
                p.AssemblyAngularVelocity = Vector3.zero
            end
        end
        model:PivotTo(newPivot)
        for p, wasAnchored in pairs(savedAnchor) do
            if p and p.Parent then
                p.Anchored = wasAnchored
                p.AssemblyLinearVelocity = Vector3.zero
                p.AssemblyAngularVelocity = Vector3.zero
            end
        end
    end)
end

function AR2_stopCarDash()
    AR2_CFG.car.tpDash = false
    if AR2_carDashConn then AR2_carDashConn:Disconnect() AR2_carDashConn = nil end
end

-- ============================================================
-- 41. CAR
-- ============================================================
AR2_car = { busy = false, cancel = false, gen = 0, lastResult = "", target = nil }

function AR2_carBase(model)
    return model and (model:FindFirstChild("Base") or model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart"))
end

function AR2_carPick()
    local char = AR2_LocalPlayer.Character
    local humanoid = char and char:FindFirstChildWhichIsA("Humanoid")
    local seat = humanoid and humanoid.SeatPart
    local container = AR2_Workspace:FindFirstChild("Vehicles")
    if not container then return nil end
    if seat then
        local node = seat
        while node and node.Parent do
            if node.Parent == container then return node end
            node = node.Parent
        end
    end
    local best, bestDist = nil, math.huge
    local origin = AR2_localPosition()
    for _, model in ipairs(container:GetChildren()) do
        if model:IsA("Model") then
            local base = AR2_carBase(model)
            if base then
                local d = (base.Position - origin).Magnitude
                if d < bestDist then best, bestDist = model, d end
            end
        end
    end
    return best
end

function AR2_car.owned(model)
    local base = AR2_carBase(model)
    return base ~= nil and base.ReceiveAge == 0
end

AR2_carHealth = AR2_carHealth or function(model)
    if not model then return nil end
    local hp = nil
    pcall(function()
        local attr = model:GetAttribute("Health")
        if type(attr) == "number" then hp = attr return end
        local hv = model:FindFirstChild("Health")
        if hv and hv:IsA("ValueBase") and type(hv.Value) == "number" then hp = hv.Value return end
        local hum = model:FindFirstChildWhichIsA("Humanoid")
        if hum then hp = hum.Health return end
        local base = AR2_carBase(model)
        if base then
            local ba = base:GetAttribute("Health")
            if type(ba) == "number" then hp = ba end
        end
    end)
    return hp
end

function AR2_ejectFromVehicle()
    local char = AR2_LocalPlayer.Character
    local hum  = char and char:FindFirstChildWhichIsA("Humanoid")
    if not hum then return false end
    local seat = hum.SeatPart
    pcall(function() hum.Sit = false end)
    pcall(function()
        if seat then
            local weld = seat:FindFirstChild("SeatWeld")
            if weld then weld:Destroy() end
        end
    end)
    pcall(function() hum:ChangeState(Enum.HumanoidStateType.Jumping) end)
    return true
end

function AR2_stopVehicleVelocity(model)
    if not model then return false end
    pcall(function()
        for _, p in ipairs(model:GetDescendants()) do
            if p:IsA("BasePart") then
                p.AssemblyLinearVelocity  = Vector3.zero
                p.AssemblyAngularVelocity = Vector3.zero
            end
        end
    end)
    return true
end

AR2_carNoclipTP = { busy = false, cancel = false, gen = 0, lastResult = "" }
AR2_carNoclipRoot       = nil
AR2_carNoclipOffsets    = nil
AR2_carNoclipSaved      = nil
AR2_carNoclipModel      = nil
AR2_carNoclipCharOffset = nil

function AR2_carNoclipSetEnabled(enabled, anchor)
    if not enabled then
        if AR2_carNoclipSaved then
            for part, s in pairs(AR2_carNoclipSaved) do
                pcall(function()
                    if part and part.Parent then
                        part.AssemblyLinearVelocity  = Vector3.zero
                        part.AssemblyAngularVelocity = Vector3.zero
                        part.CanCollide = s.canCollide
                        part.Anchored   = s.anchored
                    end
                end)
            end
        end
        AR2_carNoclipSaved      = nil
        AR2_carNoclipOffsets    = nil
        AR2_carNoclipRoot       = nil
        AR2_carNoclipModel      = nil
        AR2_carNoclipCharOffset = nil
        AR2_CFG.misc.carNoclip  = false
        return true
    end

    local model = AR2_carPick()
    if not model then return false end
    local root = AR2_carBase(model)
    if not (root and root:IsA("BasePart")) then return false end

    local saved, offsets = {}, {}
    local rootCF = root.CFrame
    local count = 0

    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then
            saved[d] = { canCollide = d.CanCollide, anchored = d.Anchored }
            offsets[d] = rootCF:Inverse() * d.CFrame
            count = count + 1
            pcall(function()
                d.AssemblyLinearVelocity  = Vector3.zero
                d.AssemblyAngularVelocity = Vector3.zero
                d.CanCollide = false
                if anchor then d.Anchored = true end
            end)
        end
    end
    if count == 0 then return false end

    AR2_carNoclipCharOffset = nil
    if anchor then
        pcall(function()
            local char = AR2_LocalPlayer.Character
            local hrp  = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then AR2_carNoclipCharOffset = rootCF:Inverse() * hrp.CFrame end
        end)
    end

    AR2_carNoclipModel   = model
    AR2_carNoclipRoot    = root
    AR2_carNoclipOffsets = offsets
    AR2_carNoclipSaved   = saved
    AR2_CFG.misc.carNoclip = true
    return true
end

AR2_carNoclipDriveConn = nil

local function AR2_carNoclipTPMoveTo(newCF)
    local r = AR2_carNoclipRoot
    if not r or not r.Parent then return end
    r.CFrame = newCF
    pcall(function()
        r.AssemblyLinearVelocity  = Vector3.zero
        r.AssemblyAngularVelocity = Vector3.zero
    end)
    for part, offset in pairs(AR2_carNoclipOffsets or {}) do
        if part and part.Parent and part ~= r then
            part.CFrame = newCF * offset
        end
    end
    if AR2_carNoclipCharOffset then
        pcall(function()
            local char = AR2_LocalPlayer.Character
            local hrp  = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.CFrame = newCF * AR2_carNoclipCharOffset
                hrp.AssemblyLinearVelocity  = Vector3.zero
                hrp.AssemblyAngularVelocity = Vector3.zero
            end
        end)
    end
end

local function AR2_carNoclipDriveStep(dt)
    local r = AR2_carNoclipRoot
    if not r or not r.Parent then return end
    local cam = AR2_Workspace.CurrentCamera
    if not cam then return end

    local speed = math.clamp(AR2_CFG.car.noclipSpeed or 90, 10, 1000)
    local uis = AR2_UserInputService

    local dir = Vector3.zero
    if uis:IsKeyDown(Enum.KeyCode.W) then dir = dir + cam.CFrame.LookVector end
    if uis:IsKeyDown(Enum.KeyCode.S) then dir = dir - cam.CFrame.LookVector end
    if uis:IsKeyDown(Enum.KeyCode.D) then dir = dir + cam.CFrame.RightVector end
    if uis:IsKeyDown(Enum.KeyCode.A) then dir = dir - cam.CFrame.RightVector end
    dir = Vector3.new(dir.X, 0, dir.Z)
    if dir.Magnitude > 0.01 then dir = dir.Unit else dir = Vector3.zero end

    local vert = 0
    if uis:IsKeyDown(Enum.KeyCode.Space) then vert = vert + 1 end
    if uis:IsKeyDown(Enum.KeyCode.LeftControl)
    or uis:IsKeyDown(Enum.KeyCode.Q) then vert = vert - 1 end

    local move = (dir * speed * dt) + Vector3.new(0, vert * speed * dt, 0)
    if move.Magnitude < 0.0001 then
        AR2_carNoclipTPMoveTo(r.CFrame)
        return
    end

    local baseCF = r.CFrame - r.CFrame.Position
    AR2_carNoclipTPMoveTo(CFrame.new(r.Position + move) * baseCF)
end

function AR2_carNoclipDriveStart()
    if AR2_carNoclipDriveConn then return end
    AR2_carNoclipDriveConn = AR2_RunService.Heartbeat:Connect(function(dt)
        if not AR2_CFG.misc.carNoclip then return end
        if AR2_carNoclipTP.busy then return end
        pcall(AR2_carNoclipDriveStep, dt)
    end)
end

function AR2_carNoclipDriveStop()
    if AR2_carNoclipDriveConn then
        pcall(function() AR2_carNoclipDriveConn:Disconnect() end)
        AR2_carNoclipDriveConn = nil
    end
end

function AR2_carNoclipStop()
    AR2_carNoclipDriveStop()
    return AR2_carNoclipSetEnabled(false)
end

function AR2_carNoclipTP.run(goalPosition, label)
    AR2_carNoclipTP.gen = (AR2_carNoclipTP.gen or 0) + 1
    local mine = AR2_carNoclipTP.gen
    AR2_carNoclipTP.cancel = true

    task.spawn(function()
        local runOk, runErr = pcall(function()
        while AR2_carNoclipTP.busy do task.wait() end
        if AR2_carNoclipTP.gen ~= mine then return end

        AR2_carNoclipTP.busy, AR2_carNoclipTP.cancel = true, false
        local started = os.clock()
        local tag = label or "car TP"

        if not AR2_inVehicle() then
            AR2_carNoclipTP.lastResult = tag .. " -- not in a vehicle"
            AR2_carNoclipTP.busy = false
            return
        end

        local model = AR2_carPick()
        if not model then
            AR2_carNoclipTP.lastResult = tag .. " -- no vehicle found"
            AR2_carNoclipTP.busy = false
            return
        end

        local wasNoclip = AR2_CFG.misc.carNoclip == true
        pcall(AR2_carNoclipDriveStop)
        pcall(AR2_carNoclipSetEnabled, false)
        task.wait(0.05)
        local okCall, ok = pcall(AR2_carNoclipSetEnabled, true, true)
        if not okCall then
            AR2_carNoclipTP.lastResult = tag .. " -- noclip error: " .. tostring(ok)
            AR2_carNoclipTP.busy = false
            return
        end
        if not ok then
            AR2_carNoclipTP.lastResult = tag .. " -- could not enable noclip"
            AR2_carNoclipTP.busy = false
            return
        end
        if not AR2_carNoclipRoot then
            AR2_carNoclipTP.lastResult = tag .. " -- no car root part"
            pcall(AR2_carNoclipSetEnabled, false)
            AR2_carNoclipTP.busy = false
            return
        end

        local vertOffset = AR2_CFG.car.noclipTPVertOffset or 5
        local goal = Vector3.new(goalPosition.X, goalPosition.Y + vertOffset, goalPosition.Z)

        local speed   = math.max(50, AR2_CFG.car.noclipTPSpeed or 800)
        local maxSecs = math.max(2, AR2_CFG.car.noclipTPMaxSeconds or 15)
        local deadline = os.clock() + maxSecs
        local reached = false

        while os.clock() < deadline do
            if AR2_carNoclipTP.cancel or AR2_carNoclipTP.gen ~= mine then break end
            local r = AR2_carNoclipRoot
            if not r or not r.Parent then break end

            local delta = goal - r.Position
            local dist  = delta.Magnitude
            if dist <= 2 then
                reached = true
                break
            end

            local step = math.min(speed * (1/60), dist)
            local baseCF = r.CFrame - r.CFrame.Position
            AR2_carNoclipTPMoveTo(CFrame.new(r.Position + delta.Unit * step) * baseCF)

            AR2_RunService.Heartbeat:Wait()
        end

        if reached and AR2_carNoclipRoot and AR2_carNoclipRoot.Parent then
            pcall(function()
                local r = AR2_carNoclipRoot
                local params = RaycastParams.new()
                params.FilterType = Enum.RaycastFilterType.Exclude
                params.FilterDescendantsInstances = {
                    AR2_carNoclipModel, AR2_LocalPlayer.Character
                }
                local hit = AR2_Workspace:Raycast(
                    r.Position + Vector3.new(0, 20, 0),
                    Vector3.new(0, -400, 0), params)
                if hit then
                    local baseCF = r.CFrame - r.CFrame.Position
                    local clearance = (r.Size.Y * 0.5) + 2
                    AR2_carNoclipTPMoveTo(
                        CFrame.new(hit.Position + Vector3.new(0, clearance, 0)) * baseCF)
                end
            end)
            task.wait(0.1)
        end

        if wasNoclip then
            pcall(AR2_carNoclipSetEnabled, true, true)
            pcall(AR2_carNoclipDriveStart)
        else
            pcall(AR2_carNoclipSetEnabled, false)
        end

        local elapsed = os.clock() - started
        if reached then
            AR2_carNoclipTP.lastResult = string.format("%s -- arrived in %.1fs", tag, elapsed)
        else
            AR2_carNoclipTP.lastResult = string.format("%s -- timeout after %.1fs", tag, elapsed)
        end
        AR2_carNoclipTP.busy = false
        end)
        if not runOk then
            AR2_carNoclipTP.lastResult = "car TP error: " .. tostring(runErr)
            AR2_carNoclipTP.busy = false
            pcall(AR2_carNoclipSetEnabled, false)
        end
    end)
end

function AR2_carNoclipTP.toPlayer()
    local wanted = AR2_CFG.tp.target
    if wanted == "-" then AR2_carNoclipTP.lastResult = "no player picked" return end
    local root = AR2_tpTargetRoot()
    if not root then AR2_carNoclipTP.lastResult = "target not found" return end
    AR2_carNoclipTP.run(root.Position, wanted)
end

function AR2_carNoclipTP.toPosition(position, label)
    AR2_carNoclipTP.run(position, label or "map")
end

function AR2_carNoclipTP.stop()
    AR2_carNoclipTP.cancel = true
    AR2_carNoclipTP.gen = (AR2_carNoclipTP.gen or 0) + 1
end

-- Car tuning
AR2_carMods = { running = false, touched = 0, note = "off" }
AR2_touched = {}
AR2_carLoopGen = 0

function AR2_carConfig(model)
    local module = model:FindFirstChild("Config")
    if not (module and module:IsA("ModuleScript")) then return nil end
    local ok, tbl = pcall(require, module)
    return (ok and type(tbl) == "table") and tbl or nil
end

function AR2_forEachDamageMod(cfg, fn)
    if type(cfg.Health) ~= "table" then return end
    for _, group in pairs(cfg.Health) do
        if type(group) == "table" and type(group.DamageModifiers) == "table" then
            for key in pairs(group.DamageModifiers) do fn(group.DamageModifiers, key) end
        end
        if type(group) == "table" and not group.DamageModifiers then
            for _, sub in pairs(group) do
                if type(sub) == "table" and type(sub.DamageModifiers) == "table" then
                    for key in pairs(sub.DamageModifiers) do fn(sub.DamageModifiers, key) end
                end
            end
        end
    end
end

function AR2_snapshot(cfg)
    if AR2_touched[cfg] then return AR2_touched[cfg] end
    local saved = { torques = {}, grips = {}, dmg = {} }
    if type(cfg.Fuel) == "table" then saved.burnRate = cfg.Fuel.BurnRate end
    if type(cfg.Physics) == "table" then
        saved.driveSpeed = cfg.Physics.DriveSpeed
        saved.reverseSpeed = cfg.Physics.ReverseSpeed

        if type(cfg.Physics.Wheels) == "table" then
            for name, wheel in pairs(cfg.Physics.Wheels) do
                if type(wheel) == "table"
                   and type(wheel.Shock) == "table"
                   and wheel.Shock.DriveMotorTorque then
                    saved.torques[name] = wheel.Shock.DriveMotorTorque
                end
            end
        end

        if type(cfg.Physics.MaterialGrip) == "table" then
            for name, material in pairs(cfg.Physics.MaterialGrip) do
                if type(material) == "table" then
                    saved.grips[name] = {
                        Speed    = material.Speed,
                        Friction = material.Friction,
                    }
                end
            end
        end
    end
    if type(cfg.ExitDamage) == "table" then saved.exitMax = cfg.ExitDamage.MaxSpeed end
    AR2_forEachDamageMod(cfg, function(t, k) saved.dmg[#saved.dmg+1] = { t = t, k = k, v = t[k] } end)
    AR2_touched[cfg] = saved
    return saved
end

function AR2_applyTo(cfg, model)
    local c = AR2_CFG.car
    local saved = AR2_snapshot(cfg)

    if c.boost and type(cfg.Physics) == "table" then
        cfg.Physics.DriveSpeed   = c.speed
        cfg.Physics.ReverseSpeed = math.floor(c.speed * 0.4)

        if type(cfg.Physics.Wheels) == "table" then
            for name, wheel in pairs(cfg.Physics.Wheels) do
                if type(wheel) == "table"
                   and type(wheel.Shock) == "table"
                   and saved.torques[name] then
                    wheel.Shock.DriveMotorTorque = saved.torques[name] * c.torque
                end
            end
        end

        if type(cfg.ExitDamage) == "table" then
            cfg.ExitDamage.MaxSpeed = 99999
        end
    end

    if c.maxTraction and type(cfg.Physics) == "table"
       and type(cfg.Physics.MaterialGrip) == "table" then
        for _, mat in pairs(cfg.Physics.MaterialGrip) do
            if type(mat) == "table" then
                if mat.Speed    ~= nil then mat.Speed    = 1 end
                if mat.Friction ~= nil then mat.Friction = 1 end
            end
        end
    end

    if c.fuel and type(cfg.Fuel) == "table" then
        cfg.Fuel.BurnRate        = 0
        cfg.Fuel.ConsumptionRate = 0
    end

    if c.god and type(cfg.Health) == "table" then
        local function zeroDmg(t)
            if type(t) ~= "table" then return end
            if type(t.DamageModifiers) == "table" then
                for k in pairs(t.DamageModifiers) do
                    t.DamageModifiers[k] = 0
                end
            else
                for _, v in pairs(t) do zeroDmg(v) end
            end
        end
        zeroDmg(cfg.Health)
    end
end

function AR2_restoreOne(cfg)
    local saved = AR2_touched[cfg]
    if not saved then return end
    if type(cfg.Fuel) == "table" and saved.burnRate ~= nil then cfg.Fuel.BurnRate = saved.burnRate end
    if type(cfg.Physics) == "table" then
        if saved.driveSpeed ~= nil then cfg.Physics.DriveSpeed = saved.driveSpeed end
        if saved.reverseSpeed ~= nil then cfg.Physics.ReverseSpeed = saved.reverseSpeed end
        if type(cfg.Physics.Wheels) == "table" then
            for name, wheel in pairs(cfg.Physics.Wheels) do
                if type(wheel) == "table"
                   and type(wheel.Shock) == "table"
                   and saved.torques[name] then
                    wheel.Shock.DriveMotorTorque = saved.torques[name]
                end
            end
        end
        if type(cfg.Physics.MaterialGrip) == "table" then
            for name, material in pairs(cfg.Physics.MaterialGrip) do
                if type(material) == "table" and saved.grips[name] then
                    if saved.grips[name].Speed    ~= nil then material.Speed    = saved.grips[name].Speed    end
                    if saved.grips[name].Friction ~= nil then material.Friction = saved.grips[name].Friction end
                end
            end
        end
    end
    if type(cfg.ExitDamage) == "table" and saved.exitMax ~= nil then cfg.ExitDamage.MaxSpeed = saved.exitMax end
    for _, entry in ipairs(saved.dmg) do entry.t[entry.k] = entry.v end
    AR2_touched[cfg] = nil
end

function AR2_restoreAll()
    for cfg in pairs(AR2_touched) do AR2_restoreOne(cfg) end
    AR2_carMods.touched = 0
end

function AR2_occupiedCar()
    local char = AR2_LocalPlayer.Character
    local hrp  = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    local vehicles = AR2_Workspace:FindFirstChild("Vehicles")
    if not vehicles then return nil end

    for _, model in ipairs(vehicles:GetChildren()) do
        if model:IsA("Model") then
            for _, part in ipairs(model:GetDescendants()) do
                if part:IsA("BasePart") then
                    for _, weld in ipairs(part:GetChildren()) do
                        if (weld:IsA("Weld") or weld:IsA("WeldConstraint"))
                        and (weld.Part0 == hrp or weld.Part1 == hrp) then
                            return model
                        end
                    end
                end
            end
        end
    end

    local best, bestDist = nil, 12
    for _, model in ipairs(vehicles:GetChildren()) do
        if model:IsA("Model") then
            local base = AR2_carBase(model)
            if base then
                local d = (base.Position - hrp.Position).Magnitude
                if d < bestDist then best = model bestDist = d end
            end
        end
    end
    return best
end

function AR2_sweep()
    local vehicles = AR2_Workspace:FindFirstChild("Vehicles")
    if not vehicles then AR2_carMods.note = "no Vehicles folder" return end
    local count = 0
    if AR2_CFG.car.allCars then
        for _, model in ipairs(vehicles:GetChildren()) do
            if model:IsA("Model") then
                local cfg = AR2_carConfig(model)
                if cfg then AR2_applyTo(cfg, model) count = count + 1 end
            end
        end
    else
        local model = AR2_occupiedCar()
        local cfg = model and AR2_carConfig(model)
        if cfg then AR2_applyTo(cfg, model) count = count + 1 end
    end
    AR2_carMods.touched = count
    local parts_ = {}
    if AR2_CFG.car.fuel then parts_[#parts_+1] = "fuel" end
    if AR2_CFG.car.boost then parts_[#parts_+1] = "boost" end
    if AR2_CFG.car.god then parts_[#parts_+1] = "god" end
    if AR2_CFG.car.maxTraction then parts_[#parts_+1] = "traction" end
    AR2_carMods.note = string.format("%s  --  %d car%s tuned%s", table.concat(parts_, ", "), count, count == 1 and "" or "s", AR2_CFG.car.allCars and "  (all)" or "  (yours)")
end

function AR2_carMods.apply()
    local wanted = AR2_CFG.car.fuel or AR2_CFG.car.boost or AR2_CFG.car.god or AR2_CFG.car.maxTraction
    AR2_restoreAll()
    AR2_carLoopGen = AR2_carLoopGen + 1
    if not wanted then
        AR2_carMods.running = false
        AR2_carMods.note = "off"
        return
    end
    AR2_carMods.running = true
    pcall(AR2_sweep)
    local mine = AR2_carLoopGen
    task.spawn(function()
        while mine == AR2_carLoopGen and AR2_carMods.running do
            task.wait(1)
            if mine ~= AR2_carLoopGen then break end
            pcall(AR2_sweep)
        end
    end)
end

function AR2_carMods.destroy()
    AR2_carLoopGen = AR2_carLoopGen + 1
    AR2_carMods.running = false
    AR2_restoreAll()
    AR2_carMods.note = "off"
end

function AR2_setBoatMode(enabled)
    AR2_CFG.car.boatMode = enabled
    local waterFolder = AR2_Workspace:FindFirstChild("Map")
        and AR2_Workspace.Map:FindFirstChild("Water")
        or  AR2_Workspace:FindFirstChild("Water")
    if not waterFolder then return end
    for _, part in ipairs(waterFolder:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            if enabled then
                if part:HasTag("Swim Surface") then
                    part:RemoveTag("Swim Surface")
                    part:AddTag("Boat Surface")
                end
            else
                if part:HasTag("Boat Surface") then
                    part:RemoveTag("Boat Surface")
                    part:AddTag("Swim Surface")
                end
            end
        end
    end
end

AR2_dragSaved   = {}
AR2_dragVehicle = nil

function AR2_setRemoveDrag(enabled)
    AR2_CFG.car.removeDrag = enabled
    local model = AR2_carPick()
    if not model then return end
    local cfg = AR2_carConfig(model)
    if not cfg then return end

    if enabled then
        AR2_dragSaved   = {}
        AR2_dragVehicle = model
        if type(cfg.Physics) == "table" then
            if type(cfg.Physics.MaterialGrip) == "table" then
                for matName, mat in pairs(cfg.Physics.MaterialGrip) do
                    if type(mat) == "table" then
                        if mat.Speed ~= nil then
                            AR2_dragSaved["gs_"..matName] = mat.Speed
                            mat.Speed = 1
                        end
                        if mat.Friction ~= nil then
                            AR2_dragSaved["gf_"..matName] = mat.Friction
                            mat.Friction = 1
                        end
                    end
                end
            end
            if type(cfg.Physics.Wheels) == "table" then
                for wname, wheel in pairs(cfg.Physics.Wheels) do
                    if type(wheel) == "table" then
                        if wheel.Elasticity ~= nil then
                            AR2_dragSaved["we_"..tostring(wname)] = wheel.Elasticity
                            wheel.Elasticity = 0
                        end
                        if wheel.Friction ~= nil then
                            AR2_dragSaved["wf_"..tostring(wname)] = wheel.Friction
                            wheel.Friction = 1
                        end
                    end
                end
            end
        end
    else
        if AR2_dragVehicle then
            local cfgOld = AR2_carConfig(AR2_dragVehicle)
            if cfgOld and cfgOld.Physics then
                if type(cfgOld.Physics.MaterialGrip) == "table" then
                    for matName, mat in pairs(cfgOld.Physics.MaterialGrip) do
                        if type(mat) == "table" then
                            if AR2_dragSaved["gs_"..matName] then mat.Speed    = AR2_dragSaved["gs_"..matName] end
                            if AR2_dragSaved["gf_"..matName] then mat.Friction = AR2_dragSaved["gf_"..matName] end
                        end
                    end
                end
                if type(cfgOld.Physics.Wheels) == "table" then
                    for wname, wheel in pairs(cfgOld.Physics.Wheels) do
                        if type(wheel) == "table" then
                            local we = AR2_dragSaved["we_"..tostring(wname)]
                            local wf = AR2_dragSaved["wf_"..tostring(wname)]
                            if we then wheel.Elasticity = we end
                            if wf then wheel.Friction   = wf end
                        end
                    end
                end
            end
        end
        AR2_dragSaved, AR2_dragVehicle = {}, nil
    end
end

AR2_steerSaved   = {}
AR2_steerApplied = false
AR2_steerConn    = nil

function AR2_setFullSteer(enabled)
    AR2_CFG.car.fullSteer = enabled
    if enabled then
        if AR2_steerConn then return end
        AR2_steerConn = AR2_RunService.Heartbeat:Connect(function()
            if not AR2_CFG.car.fullSteer then return end
            local model = AR2_carPick()
            if not model then return end
            local cfg = AR2_carConfig(model)
            if not cfg or not cfg.Physics then return end

            if not AR2_steerApplied then
                AR2_steerApplied = true
                for _, k in ipairs({"FullSteeringUntil","NoSteeringAfter","SteerResponce","TurnSpeed"}) do
                    if cfg.Physics[k] ~= nil then AR2_steerSaved[k] = cfg.Physics[k] end
                end
                if type(cfg.Physics.Wheels) == "table" then
                    for wname, wheel in pairs(cfg.Physics.Wheels) do
                        if type(wheel) == "table" and wheel.SteerAngle ~= nil then
                            AR2_steerSaved["sa_"..tostring(wname)] = wheel.SteerAngle
                        end
                    end
                end
            end

            if cfg.Physics.FullSteeringUntil ~= nil then cfg.Physics.FullSteeringUntil = 9999 end
            if cfg.Physics.NoSteeringAfter   ~= nil then cfg.Physics.NoSteeringAfter   = 99999 end
            if cfg.Physics.SteerResponce     ~= nil then cfg.Physics.SteerResponce     = math.max(8, cfg.Physics.SteerResponce) end
            if type(cfg.Physics.Wheels) == "table" then
                for wname, wheel in pairs(cfg.Physics.Wheels) do
                    if type(wheel) == "table" and wheel.SteerAngle ~= nil then
                        local orig = AR2_steerSaved["sa_"..tostring(wname)] or 0
                        if orig > 0 then wheel.SteerAngle = math.max(orig, 45)
                        elseif orig < 0 then wheel.SteerAngle = math.min(orig, -45) end
                    end
                end
            end
        end)
    else
        if AR2_steerConn then AR2_steerConn:Disconnect() AR2_steerConn = nil end
        if AR2_steerApplied then
            local model = AR2_carPick()
            local cfg = model and AR2_carConfig(model)
            if cfg and cfg.Physics then
                for k, v in pairs(AR2_steerSaved) do
                    if type(k) == "string" and k:sub(1,3) ~= "sa_" then
                        cfg.Physics[k] = v
                    end
                end
                if type(cfg.Physics.Wheels) == "table" then
                    for wname, wheel in pairs(cfg.Physics.Wheels) do
                        if type(wheel) == "table" then
                            local saved = AR2_steerSaved["sa_"..tostring(wname)]
                            if saved then wheel.SteerAngle = saved end
                        end
                    end
                end
            end
            AR2_steerSaved, AR2_steerApplied = {}, false
        end
    end
end

AR2_afsEnabled = false
AR2_afsConn    = nil

function AR2_setAntiFallStun(enabled)
    AR2_CFG.misc.antiFallStun = enabled
    AR2_afsEnabled = enabled

    if AR2_afsConn then AR2_afsConn:Disconnect() AR2_afsConn = nil end
    if not enabled then return end

    AR2_afsConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_afsEnabled then return end
        local gc = AR2_gameChar
        if type(gc) ~= "table" then return end
        pcall(function()
            if rawget(gc, "FallImpacting") ~= nil then rawset(gc, "FallImpacting", false) end
            if rawget(gc, "Staggered")     ~= nil then rawset(gc, "Staggered", false)     end
            local rt = rawget(gc, "RagdollTimer")
            if type(rt) == "number" and rt > 0 then rawset(gc, "RagdollTimer", 0) end
        end)
    end)
end

-- ============================================================
-- 42. FLY (personagem)
-- ============================================================
local AR2_flyActive       = false
local AR2_flyConn         = nil
local AR2_flyVel          = Vector3.zero
local AR2_flyLastPos      = nil
local AR2_flySnapSlowdown = 1.0
local AR2_flySnapCount    = 0
local AR2_flyRevalidating = false
local AR2_flyRevalUntil   = 0
local AR2_flyBurstFrame   = 0
local AR2_noFallConn      = nil

local AR2_FLY_MAX_SPEED   = 1000
local AR2_FLY_MIN_SPEED   = 10
local AR2_FLY_SNAP_LIMIT  = 5
local AR2_FLY_REVAL_PAUSE = 0.6

AR2_flySpeed = math.clamp(AR2_CFG.misc.flySpeedPct or 300, AR2_FLY_MIN_SPEED, AR2_FLY_MAX_SPEED)
AR2_CFG.misc.flySpeedPct = AR2_flySpeed

local function AR2_flyGetRoot()
    local char = AR2_LocalPlayer.Character
    return char and char:FindFirstChild("HumanoidRootPart")
end

local function AR2_flyGetHum()
    local char = AR2_LocalPlayer.Character
    return char and char:FindFirstChildWhichIsA("Humanoid")
end

local function AR2_flyStartNoFall()
    if AR2_noFallConn then return end
    AR2_noFallConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_flyActive then return end
        local char = AR2_LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local vy = root.AssemblyLinearVelocity.Y
        if vy >= -30 then return end
        local rp = RaycastParams.new()
        rp.FilterType = Enum.RaycastFilterType.Exclude
        rp.FilterDescendantsInstances = { char }
        local hit = AR2_Workspace:Raycast(root.Position, Vector3.new(0,-15,0), rp)
        if hit then
            root.AssemblyLinearVelocity = Vector3.new(
                root.AssemblyLinearVelocity.X, 0,
                root.AssemblyLinearVelocity.Z)
        else
            root.AssemblyLinearVelocity = Vector3.new(
                root.AssemblyLinearVelocity.X,
                math.max(vy * 0.88, -25),
                root.AssemblyLinearVelocity.Z)
        end
    end)
end

function AR2_stopFly()
    AR2_flyActive       = false
    AR2_flyLastPos      = nil
    AR2_flyVel          = Vector3.zero
    AR2_flySnapCount    = 0
    AR2_flyRevalidating = false
    AR2_flyBurstFrame   = 0
    if AR2_flyConn then AR2_flyConn:Disconnect() AR2_flyConn = nil end

    local root = AR2_flyGetRoot()
    if root then
        local rp = RaycastParams.new()
        rp.FilterType = Enum.RaycastFilterType.Exclude
        rp.FilterDescendantsInstances = { AR2_LocalPlayer.Character }
        local hit = AR2_Workspace:Raycast(root.Position, Vector3.new(0,-2000,0), rp)
        if hit then
            root.CFrame = CFrame.new(hit.Position + Vector3.new(0, 3.5, 0))
                * (root.CFrame - root.CFrame.Position)
        end
        root.AssemblyLinearVelocity  = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
    end

    task.delay(0.2, function()
        local h = AR2_flyGetHum()
        if h then
            pcall(function()
                h:SetStateEnabled(Enum.HumanoidStateType.Landed,     true)
                h:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
                h:SetStateEnabled(Enum.HumanoidStateType.GettingUp,   true)
            end)
        end
    end)
end

function AR2_startFly()
    if AR2_flyActive then return end
    if AR2_flyConn then
        AR2_flyConn:Disconnect()
        AR2_flyConn = nil
    end

    local root = AR2_flyGetRoot()
    local hum  = AR2_flyGetHum()
    if not root or not hum then return end

    AR2_flySpeed = math.clamp(AR2_CFG.misc.flySpeedPct or AR2_flySpeed or 300, AR2_FLY_MIN_SPEED, AR2_FLY_MAX_SPEED)
    AR2_CFG.misc.flySpeedPct = AR2_flySpeed

    root.CFrame = root.CFrame + Vector3.new(0, 3, 0)
    root.AssemblyLinearVelocity = Vector3.new(0, 10, 0)
    task.wait(0.1)

    pcall(function()
        hum:SetStateEnabled(Enum.HumanoidStateType.Landed,      false)
        hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        hum:SetStateEnabled(Enum.HumanoidStateType.GettingUp,   true)
        hum:ChangeState(Enum.HumanoidStateType.GettingUp)
    end)

    AR2_flyLastPos      = root.Position
    AR2_flyVel          = Vector3.zero
    AR2_flySnapSlowdown = 1.0
    AR2_flySnapCount    = 0
    AR2_flyRevalidating = false
    AR2_flyBurstFrame   = 0
    AR2_flyActive       = true

    AR2_flyConn = AR2_RunService.Heartbeat:Connect(function(dt)
        if not AR2_CFG.misc.fly then AR2_stopFly() return end
        local r = AR2_flyGetRoot()
        local h = AR2_flyGetHum()
        if not r then AR2_stopFly() return end

        pcall(function()
            h:SetStateEnabled(Enum.HumanoidStateType.Landed,     false)
            h:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
            local state = h:GetState()
            if state == Enum.HumanoidStateType.Landed
            or state == Enum.HumanoidStateType.FallingDown then
                h:ChangeState(Enum.HumanoidStateType.GettingUp)
            end
        end)

        if AR2_flyRevalidating then
            if os.clock() < AR2_flyRevalUntil then
                r.AssemblyLinearVelocity = Vector3.new(
                    (math.random() - 0.5) * 2, -2, (math.random() - 0.5) * 2)
                AR2_flyLastPos = r.Position
                return
            else
                AR2_flyRevalidating = false
                AR2_flySnapSlowdown = 0.4
                AR2_flyVel          = Vector3.zero
            end
        end

        if AR2_flyLastPos then
            local actualSpeed   = (r.Position - AR2_flyLastPos).Magnitude / math.max(dt, 0.001)
            local expectedSpeed = AR2_flyVel.Magnitude
            if expectedSpeed > 15 and actualSpeed < expectedSpeed * 0.25 then
                AR2_flySnapSlowdown = math.max(0.15, AR2_flySnapSlowdown - 0.2)
                AR2_flySnapCount    = AR2_flySnapCount + 1
                if AR2_flySnapCount >= AR2_FLY_SNAP_LIMIT then
                    AR2_flyRevalidating = true
                    AR2_flyRevalUntil   = os.clock() + AR2_FLY_REVAL_PAUSE
                    AR2_flySnapCount    = 0
                    AR2_flyVel          = Vector3.zero
                    AR2_flyLastPos      = r.Position
                    return
                end
            else
                AR2_flySnapSlowdown = math.min(1.0, AR2_flySnapSlowdown + 0.025)
                if AR2_flySnapCount > 0 then AR2_flySnapCount = AR2_flySnapCount - 1 end
            end
        end
        AR2_flyLastPos = r.Position

        local cam    = workspace.CurrentCamera
        local target = Vector3.zero
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.W) then target = target + cam.CFrame.LookVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.S) then target = target - cam.CFrame.LookVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.D) then target = target + cam.CFrame.RightVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.A) then target = target - cam.CFrame.RightVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.Space) then target = target + Vector3.new(0, 1, 0) end
        local descendDown = AR2_UserInputService:IsKeyDown(Enum.KeyCode.LeftControl)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.RightControl)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.Q)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.LeftShift)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.RightShift)
        if descendDown then target = target - Vector3.new(0, 1, 0) end

        local effectiveSpeed = AR2_flySpeed * AR2_flySnapSlowdown
        local targetVel = target.Magnitude > 0.01 and target.Unit * effectiveSpeed or Vector3.zero

        local lerpRate = math.min(1, dt * (10 + math.random() * 4))
        AR2_flyVel = AR2_flyVel:Lerp(targetVel, lerpRate)

        local hSpeed   = Vector3.new(AR2_flyVel.X, 0, AR2_flyVel.Z).Magnitude
        local gravBias = hSpeed > 30 and Vector3.new(0, -hSpeed * 0.04, 0) or Vector3.zero

        local noise = Vector3.zero
        if math.random(3) == 1 then
            noise = Vector3.new(
                (math.random() - 0.5) * 0.5,
                (math.random() - 0.5) * 0.5,
                (math.random() - 0.5) * 0.5)
        end

        AR2_flyBurstFrame = (AR2_flyBurstFrame + 1) % 5
        if AR2_flyBurstFrame == 0
        and not AR2_UserInputService:IsKeyDown(Enum.KeyCode.Space)
        and AR2_flyVel.Magnitude > 10 then
            r.AssemblyLinearVelocity = AR2_flyVel * 0.6 + gravBias
        else
            r.AssemblyLinearVelocity = AR2_flyVel + gravBias + noise
        end
    end)
end

function AR2_setFly(enabled)
    AR2_CFG.misc.fly = enabled
    if enabled then
        if not AR2_flyActive then AR2_startFly() end
    else
        AR2_stopFly()
    end
end

function AR2_resetFallState()
    AR2_CFG.misc.fly = false
    AR2_flyActive       = false
    AR2_flyRevalidating = false
    AR2_flySnapCount    = 0
    AR2_flyVel          = Vector3.zero
    AR2_flyLastPos      = nil
    AR2_flyBurstFrame   = 0
    if AR2_flyConn then AR2_flyConn:Disconnect() AR2_flyConn = nil end

    local char = AR2_LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum  = char and char:FindFirstChildWhichIsA("Humanoid")

    if not root then return end

    root.AssemblyLinearVelocity  = Vector3.zero
    root.AssemblyAngularVelocity = Vector3.zero

    if hum then
        pcall(function()
            for _, state in ipairs({
                Enum.HumanoidStateType.Landed,
                Enum.HumanoidStateType.FallingDown,
                Enum.HumanoidStateType.GettingUp,
                Enum.HumanoidStateType.Running,
                Enum.HumanoidStateType.RunningNoPhysics,
                Enum.HumanoidStateType.Jumping,
                Enum.HumanoidStateType.Physics,
            }) do
                hum:SetStateEnabled(state, true)
            end
        end)
        hum.Sit           = false
        hum.PlatformStand = false
        hum.AutoRotate    = true
    end

    local rp = RaycastParams.new()
    rp.FilterType = Enum.RaycastFilterType.Exclude
    rp.FilterDescendantsInstances = { char }
    local hit = AR2_Workspace:Raycast(root.Position, Vector3.new(0, -2000, 0), rp)
    if hit then
        root.CFrame = CFrame.new(hit.Position + Vector3.new(0, 3.5, 0))
            * (root.CFrame - root.CFrame.Position)
    end

    task.delay(0.1, function()
        local h = AR2_flyGetHum()
        if h then
            pcall(function() h:ChangeState(Enum.HumanoidStateType.GettingUp) end)
        end
    end)

    local cam = workspace.CurrentCamera
    if cam and hum then
        pcall(function()
            cam.CameraSubject = hum
            cam.CameraType    = Enum.CameraType.Custom
        end)
    end
end

AR2_flyStartNoFall()

AR2_LocalPlayer.CharacterAdded:Connect(function(char)
    if AR2_flyConn then AR2_flyConn:Disconnect() AR2_flyConn = nil end
    AR2_flyActive       = false
    AR2_flyVel          = Vector3.zero
    AR2_flyLastPos      = nil
    AR2_flySnapCount    = 0
    AR2_flyRevalidating = false
    AR2_flyBurstFrame   = 0
    AR2_CFG.misc.fly    = false

    local hum = char:FindFirstChildWhichIsA("Humanoid")
    if hum then
        pcall(function()
            hum.PlatformStand = false
            hum.AutoRotate    = true
            hum.Sit           = false
            hum:SetStateEnabled(Enum.HumanoidStateType.Landed,     true)
            hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
            hum:SetStateEnabled(Enum.HumanoidStateType.GettingUp,   true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Freefall,    true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Running,     true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Jumping,     true)
        end)
    end

    task.wait(1.0)
    local hum2 = char:FindFirstChildWhichIsA("Humanoid")
    if hum2 then
        pcall(function()
            hum2.PlatformStand = false
            hum2.AutoRotate    = true
            hum2:SetStateEnabled(Enum.HumanoidStateType.Landed,     true)
            hum2:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
            hum2:SetStateEnabled(Enum.HumanoidStateType.GettingUp,   true)
            hum2:SetStateEnabled(Enum.HumanoidStateType.Freefall,    true)
        end)
    end
end)

local function _flyKillState()
    if AR2_flyConn then AR2_flyConn:Disconnect() AR2_flyConn = nil end
    AR2_flyActive       = false
    AR2_flyVel          = Vector3.zero
    AR2_flyLastPos      = nil
    AR2_flyRevalidating = false
    AR2_CFG.misc.fly    = false
end
if AR2_LocalPlayer.Character then
    local h = AR2_LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid")
    if h then h.Died:Connect(_flyKillState) end
end
AR2_LocalPlayer.CharacterAdded:Connect(function(c)
    task.wait(0.5)
    local h = c:FindFirstChildWhichIsA("Humanoid")
    if h then h.Died:Connect(_flyKillState) end
end)

AR2_UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if not AR2_flyActive then return end
    local k = input.KeyCode
    if k == Enum.KeyCode.Equals or k == Enum.KeyCode.KeypadPlus then
        AR2_flySpeed = math.min(AR2_flySpeed + 20, AR2_FLY_MAX_SPEED)
        AR2_CFG.misc.flySpeedPct = AR2_flySpeed
    elseif k == Enum.KeyCode.Minus or k == Enum.KeyCode.KeypadMinus then
        AR2_flySpeed = math.max(AR2_flySpeed - 20, AR2_FLY_MIN_SPEED)
        AR2_CFG.misc.flySpeedPct = AR2_flySpeed
    end
end)

AR2_UserInputService.InputChanged:Connect(function(input)
    if not AR2_flyActive then return end
    if input.UserInputType == Enum.UserInputType.MouseWheel then
        AR2_flySpeed = math.clamp(AR2_flySpeed + input.Position.Z * 20, AR2_FLY_MIN_SPEED, AR2_FLY_MAX_SPEED)
        AR2_CFG.misc.flySpeedPct = AR2_flySpeed
    end
end)

-- ============================================================
-- 43. CAR FLY
-- ============================================================
AR2_CFG.car.fly      = AR2_CFG.car.fly or false
AR2_CFG.car.flySpeed = AR2_CFG.car.flySpeed or 100

AR2_carFlyActive        = false
AR2_carFlyConn          = nil
AR2_carFlyVel           = Vector3.zero
AR2_carFlyModel         = nil
AR2_carFlyRoot          = nil
AR2_carFlySavedParts    = nil
AR2_carFlyInitialHealth = nil
AR2_carFlyHealthWatch   = 0
AR2_carFlyBV            = nil
AR2_carFlyBG            = nil

local AR2_CAR_FLY_MIN_SPEED = 10
local AR2_CAR_FLY_MAX_SPEED = 200

local function AR2_carFlyGetModel()
    local char = AR2_LocalPlayer.Character
    local hum  = char and char:FindFirstChildWhichIsA("Humanoid")
    if hum and hum.SeatPart then
        local node = hum.SeatPart
        while node and node.Parent do
            if node.Parent == AR2_Vehicles then return node end
            node = node.Parent
        end
    end
    if AR2_carPick then
        local ok, m = pcall(AR2_carPick)
        if ok then return m end
    end
    return nil
end

function AR2_carFlyStop()
    AR2_carFlyActive        = false
    AR2_CFG.car.fly         = false
    AR2_carFlyInitialHealth = nil
    if AR2_carFlyConn then AR2_carFlyConn:Disconnect() AR2_carFlyConn = nil end
    AR2_carFlyVel = Vector3.zero

    pcall(function() if AR2_carFlyBV and AR2_carFlyBV.Parent then AR2_carFlyBV:Destroy() end end)
    pcall(function() if AR2_carFlyBG and AR2_carFlyBG.Parent then AR2_carFlyBG:Destroy() end end)
    AR2_carFlyBV, AR2_carFlyBG = nil, nil

    if AR2_carFlySavedParts then
        for part, wasCollide in pairs(AR2_carFlySavedParts) do
            pcall(function()
                if part and part.Parent then
                    part.CanCollide = wasCollide
                    part.AssemblyLinearVelocity  = Vector3.zero
                    part.AssemblyAngularVelocity = Vector3.zero
                end
            end)
        end
    end
    AR2_carFlyModel      = nil
    AR2_carFlyRoot       = nil
    AR2_carFlySavedParts = nil
end

local function AR2_carFlyDamageEject()
    local model = AR2_carFlyModel
    AR2_carFlyStop()
    AR2_ejectFromVehicle()
    AR2_stopVehicleVelocity(model)
    pcall(function()
        if AR2_Notif and AR2_Notif.new then
            AR2_Notif.new({Title="Tetescr hub",Content="Car took damage — auto-ejected",Duration=3})
        end
    end)
end

function AR2_carFlyStart()
    if AR2_carFlyActive then return true end
    local model = AR2_carFlyGetModel()
    if not model then return false end
    local char = AR2_LocalPlayer.Character
    local hum  = char and char:FindFirstChildWhichIsA("Humanoid")
    if not hum or not hum.Sit then return false end

    local root = model.PrimaryPart or model:FindFirstChild("Base")
    if not root then
        for _, p in ipairs(model:GetDescendants()) do
            if p:IsA("BasePart") then root = p break end
        end
    end
    if not root then return false end

    AR2_carFlyInitialHealth = AR2_carHealth(model)
    AR2_carFlyHealthWatch   = 0

    local savedParts = {}
    for _, p in ipairs(model:GetDescendants()) do
        if p:IsA("BasePart") then
            savedParts[p] = p.CanCollide
            p.CanCollide  = false
        end
    end

    AR2_carFlyModel      = model
    AR2_carFlyRoot       = root
    AR2_carFlySavedParts = savedParts
    AR2_carFlyVel        = Vector3.zero
    AR2_carFlyActive     = true
    AR2_CFG.car.fly      = true

    pcall(function()
        local bv = Instance.new("BodyVelocity")
        bv.Name      = "AR2_carFlyBV"
        bv.MaxForce  = Vector3.new(1, 1, 1) * 1e6
        bv.P         = 1250
        bv.Velocity  = Vector3.zero
        bv.Parent    = root
        AR2_carFlyBV = bv

        local bg = Instance.new("BodyGyro")
        bg.Name       = "AR2_carFlyBG"
        bg.MaxTorque  = Vector3.new(1, 1, 1) * 1e6
        bg.P          = 3000
        bg.D          = 500
        bg.CFrame     = root.CFrame
        bg.Parent     = root
        AR2_carFlyBG  = bg
    end)
    if not AR2_carFlyBV then
        AR2_carFlyStop()
        return false
    end

    AR2_carFlyConn = AR2_RunService.Heartbeat:Connect(function(dt)
        if not AR2_CFG.car.fly then AR2_carFlyStop() return end
        local m = AR2_carFlyModel
        if not m or not m.Parent then AR2_carFlyStop() return end
        local r = AR2_carFlyRoot
        if not r or not r.Parent then AR2_carFlyStop() return end

        local c = AR2_LocalPlayer.Character
        local h = c and c:FindFirstChildWhichIsA("Humanoid")
        if not h or not h.Sit then AR2_carFlyStop() return end

        AR2_carFlyHealthWatch = AR2_carFlyHealthWatch + dt
        if AR2_carFlyHealthWatch >= 0.1 then
            AR2_carFlyHealthWatch = 0
            local hp = AR2_carHealth(m)
            if hp then
                if not AR2_carFlyInitialHealth then
                    AR2_carFlyInitialHealth = hp
                elseif hp < AR2_carFlyInitialHealth then
                    AR2_carFlyDamageEject()
                    return
                end
            end
        end

        local cam    = workspace.CurrentCamera
        local target = Vector3.zero
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.W) then target = target + cam.CFrame.LookVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.S) then target = target - cam.CFrame.LookVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.D) then target = target + cam.CFrame.RightVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.A) then target = target - cam.CFrame.RightVector end
        if AR2_UserInputService:IsKeyDown(Enum.KeyCode.Space) then target = target + Vector3.new(0, 1, 0) end
        local descend = AR2_UserInputService:IsKeyDown(Enum.KeyCode.LeftControl)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.RightControl)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.Q)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.LeftShift)
            or AR2_UserInputService:IsKeyDown(Enum.KeyCode.RightShift)
        if descend then target = target - Vector3.new(0, 1, 0) end

        AR2_CFG.car.flySpeed = math.clamp(AR2_CFG.car.flySpeed or 100, AR2_CAR_FLY_MIN_SPEED, AR2_CAR_FLY_MAX_SPEED)
        local speed = AR2_CFG.car.flySpeed
        local targetVel = target.Magnitude > 0.01 and target.Unit * speed or Vector3.zero

        AR2_carFlyVel = AR2_carFlyVel:Lerp(targetVel, math.min(1, dt * 6))

        local flatFwd
        if target.Magnitude > 0.01 then
            flatFwd = Vector3.new(targetVel.X, 0, targetVel.Z)
        else
            flatFwd = Vector3.new(cam.CFrame.LookVector.X, 0, cam.CFrame.LookVector.Z)
        end

        local bv, bg = AR2_carFlyBV, AR2_carFlyBG
        if not (bv and bv.Parent and bg and bg.Parent) then AR2_carFlyStop() return end
        bv.Velocity = AR2_carFlyVel
        if flatFwd.Magnitude > 0.01 then
            bg.CFrame = CFrame.lookAt(r.Position, r.Position + flatFwd.Unit)
        end

        pcall(function()
            if h.SeatPart and h.SeatPart:IsA("VehicleSeat") then
                h.SeatPart.ThrottleFloat = 0
                h.SeatPart.SteerFloat    = 0
            end
        end)
    end)

    return true
end

function AR2_carFlySetEnabled(on)
    if on then
        local ok = AR2_carFlyStart()
        if not ok then AR2_CFG.car.fly = false end
        return ok
    else
        AR2_carFlyStop()
        return true
    end
end

-- ============================================================
-- 44. HBE / WALLBANG / RELOAD / VEH EQUIP / AUTO RELOAD
-- ============================================================
AR2_hbeVisuals     = {}
AR2_hbeHookInstalled = false
AR2_hbeReentry     = false
AR2_hbeFolder      = nil

local function AR2_getHbeFolder()
    if AR2_hbeFolder and AR2_hbeFolder.Parent then return AR2_hbeFolder end
    AR2_hbeFolder = Instance.new("Folder")
    AR2_hbeFolder.Name = "tetescr_hbe"
    AR2_hbeFolder.Parent = AR2_Workspace
    return AR2_hbeFolder
end

local function AR2_hbeIsLocalPlayer(character)
    if not character then return false end
    if character == AR2_LocalPlayer.Character then return true end
    if AR2_Players:GetPlayerFromCharacter(character) == AR2_LocalPlayer then return true end
    if character.Name == AR2_LocalPlayer.Name then return true end
    return false
end

local function AR2_hbeAddVisual(character)
    if AR2_hbeVisuals[character] then return end
    if AR2_hbeIsLocalPlayer(character) then return end
    if not character:FindFirstChildOfClass("Humanoid") then return end

    local realHead = character:FindFirstChild("Head")
    if not realHead then return end

    local charRoot = character:FindFirstChild("HumanoidRootPart") or realHead
    if (charRoot.Position - AR2_localPosition()).Magnitude > 2000 then return end

    local v = Instance.new("Part")
    v.Name         = "HeadHitbox"
    v.Size         = Vector3.new(
        AR2_CFG.weapon.hbeSize,
        AR2_CFG.weapon.hbeSize,
        AR2_CFG.weapon.hbeSize)
    v.Transparency = 1
    v.CanCollide   = false
    v.CanTouch     = false
    v.CanQuery     = true
    v.Massless     = true
    v.Anchored     = true
    v.CastShadow   = false
    v:SetAttribute("ar2_hbe", true)
    v:SetAttribute("ar2_hbe_owner", character.Name)
    pcall(function() v.CollisionGroupId = realHead.CollisionGroupId end)
    v.CFrame       = realHead.CFrame
    v.Parent       = AR2_getHbeFolder()

    AR2_hbeVisuals[character] = v

    local guardConn
    guardConn = AR2_RunService.Heartbeat:Connect(function()
        if not v or not v.Parent or not realHead or not realHead.Parent then
            if v and v.Parent then v:Destroy() end
            if guardConn then guardConn:Disconnect() end
            AR2_hbeVisuals[character] = nil
            return
        end
        v.CFrame = realHead.CFrame
        if AR2_CollectionService:HasTag(v, "Bullets Penetrate") then
            AR2_CollectionService:RemoveTag(v, "Bullets Penetrate")
        end
    end)

    character.AncestryChanged:Connect(function()
        if not character.Parent then
            if v and v.Parent then v:Destroy() end
            if guardConn then guardConn:Disconnect() end
            AR2_hbeVisuals[character] = nil
        end
    end)
end

function AR2_hbeScan()
    if not AR2_Characters then return end
    local localRoot = AR2_localPosition()
    for _, model in ipairs(AR2_Characters:GetChildren()) do
        if model:IsA("Model") and not AR2_hbeIsLocalPlayer(model) then
            local root = model:FindFirstChild("HumanoidRootPart") or model:FindFirstChild("Head")
            if root then
                if (root.Position - localRoot).Magnitude <= 2000 then
                    AR2_hbeAddVisual(model)
                else
                    local v = AR2_hbeVisuals[model]
                    if v then v:Destroy() AR2_hbeVisuals[model] = nil end
                end
            end
        end
    end
end

function AR2_hbeRemoveAll()
    for char, v in pairs(AR2_hbeVisuals) do
        if v and v.Parent then v:Destroy() end
        AR2_hbeVisuals[char] = nil
    end
    AR2_hbeVisuals = {}
    if AR2_hbeFolder and AR2_hbeFolder.Parent then AR2_hbeFolder:Destroy() AR2_hbeFolder = nil end
end

function AR2_hbeUpdateSizes()
    for _, v in pairs(AR2_hbeVisuals) do
        if v and v.Parent then
            v.Size = Vector3.new(
                AR2_CFG.weapon.hbeSize,
                AR2_CFG.weapon.hbeSize,
                AR2_CFG.weapon.hbeSize)
        end
    end
end

function AR2_hbeInstallHook()
    if AR2_hbeHookInstalled then return end
    if not (AR2_CAP.getrawmetatable and AR2_CAP.setreadonly and AR2_CAP.getnamecallmethod) then
        AR2_logHook("HBE", false, "missing getrawmetatable/setreadonly/getnamecallmethod")
        return
    end
    AR2_hbeHookInstalled = true

    local ok, err = pcall(function()
    local mt = getrawmetatable(game)
    local originalNC = mt.__namecall
    setreadonly(mt, false)

    mt.__namecall = newcclosure(function(self, ...)
        local method = getnamecallmethod()

        if method == "Raycast" and self == workspace and AR2_CFG.weapon.hbeOn and not AR2_hbeReentry then
            local origin, direction, params = ...

            if typeof(origin) == "Vector3" and params and typeof(params) == "RaycastParams" then
                local filter = params.FilterDescendantsInstances
                if filter and #filter >= 1 then
                    local hasChars = false
                    for _, inst in ipairs(filter) do
                        if inst == AR2_Characters
                        or (inst.Name == "Characters" and inst.ClassName == "Folder") then
                            hasChars = true
                            break
                        end
                    end

                    if hasChars then
                        local cleaned = {}
                        for _, inst in ipairs(filter) do
                            if inst ~= AR2_Characters
                            and not (inst.Name == "Characters" and inst.ClassName == "Folder") then
                                table.insert(cleaned, inst)
                            end
                        end
                        local selfChar = AR2_LocalPlayer.Character
                        if selfChar then
                            table.insert(cleaned, selfChar)
                        end
                        params.FilterDescendantsInstances = cleaned
                    end
                end
            end

            local result = originalNC(self, ...)

            if result and result.Instance then
                local hit    = result.Instance
                local hpos   = result.Position
                local radius = (AR2_CFG.weapon.hbeSize or 10) * 0.75
                for char, fakeHead in pairs(AR2_hbeVisuals) do
                    if fakeHead and fakeHead.Parent and char and char.Parent then
                        if hit == fakeHead
                        or (fakeHead.Position - hpos).Magnitude < radius then
                            local realHead = char:FindFirstChild("Head")
                            if realHead and realHead.Parent then
                                return {
                                    Instance = realHead,
                                    Position = result.Position,
                                    Normal   = result.Normal,
                                    Material = result.Material,
                                    Distance = result.Distance,
                                }
                            end
                        end
                    end
                end
            end

            return result
        end

        return originalNC(self, ...)
    end)

    setreadonly(mt, true)
    end)
    if not ok then AR2_logHook("HBE", false, tostring(err)) return end
    AR2_logHook("HBE", true)

    if AR2_Characters then
        AR2_Characters.ChildAdded:Connect(function(model)
            if not model:IsA("Model") then return end
            if not AR2_CFG.weapon.hbeOn then return end
            if model.Name == AR2_LocalPlayer.Name then return end
            task.wait(0.3)
            if AR2_hbeIsLocalPlayer(model) then return end
            AR2_hbeAddVisual(model)
        end)
    end

    task.spawn(function()
        while true do
            task.wait(2)
            if AR2_CFG.weapon.hbeOn then
                pcall(AR2_hbeScan)
            end
        end
    end)
end

function AR2_hbeSetEnabled(enabled)
    AR2_CFG.weapon.hbeOn = enabled
    if enabled then
        AR2_hbeInstallHook()
        AR2_hbeScan()
    else
        AR2_hbeRemoveAll()
    end
end

-- Wallbang tagging
local wallbangTagConn = nil
local AR2_wallbangSaved = {}
local AR2_WALLBANG_GROUPS = { [7]=true, [8]=true, [9]=true, [11]=true, [17]=true }

local function isCharacterPart(part)
    local parent = part.Parent
    while parent do
        if parent:FindFirstChildOfClass("Humanoid") then return true end
        if parent:IsA("Model") and (parent.Parent == AR2_Characters or parent.Parent == AR2_Zombies) then return true end
        parent = parent.Parent
    end
    return false
end

local function tagPart(part)
    if part.Name == "ar2_hbe_head" or part:GetAttribute("ar2_hbe") then return end
    if part:IsA("BasePart") and not isCharacterPart(part) and not AR2_CollectionService:HasTag(part, "Bullets Penetrate") then
        AR2_CollectionService:AddTag(part, "Bullets Penetrate")
    end
end

local function removeCharacterTags(model)
    for _, part in ipairs(model:GetDescendants()) do
        if part:IsA("BasePart") then
            if AR2_CollectionService:HasTag(part, "Bullets Penetrate") then
                AR2_CollectionService:RemoveTag(part, "Bullets Penetrate")
            end
            local saved = AR2_wallbangSaved[part]
            if saved ~= nil then
                pcall(function() part.CollisionGroupId = saved end)
                AR2_wallbangSaved[part] = nil
            end
        end
    end
end

local function AR2_wallbangForcePart(part)
    if not part:IsA("BasePart") then return end
    if part.Name == "ar2_hbe_head" or part:GetAttribute("ar2_hbe") then return end
    if isCharacterPart(part) then return end
    if AR2_Characters and part:IsDescendantOf(AR2_Characters) then return end
    if AR2_Zombies and part:IsDescendantOf(AR2_Zombies) then return end
    if AR2_Vehicles and part:IsDescendantOf(AR2_Vehicles) then return end
    if part:IsA("VehicleSeat") or part:IsA("Seat") then return end
    if part:FindFirstAncestorWhichIsA("VehicleSeat") then return end
    local gid = part.CollisionGroupId
    if AR2_WALLBANG_GROUPS[gid] then
        AR2_wallbangSaved[part] = gid
        pcall(function() part.CollisionGroupId = 0 end)
    end
    tagPart(part)
end

function startWallbangTagging()
    if wallbangTagConn then return end

    for _, part in ipairs(AR2_Workspace:GetDescendants()) do
        AR2_wallbangForcePart(part)
    end
    local mapFolder = AR2_Workspace:FindFirstChild("Map")
    if mapFolder then
        for _, desc in ipairs(mapFolder:GetDescendants()) do
            AR2_wallbangForcePart(desc)
        end
    end

    wallbangTagConn = AR2_Workspace.DescendantAdded:Connect(function(desc)
        if desc:IsA("BasePart") then
            AR2_wallbangForcePart(desc)
        elseif desc:IsA("Humanoid") then
            local model = desc.Parent
            if model and model:IsA("Model") then removeCharacterTags(model) end
        end
    end)
end

function stopWallbangTagging()
    if wallbangTagConn then wallbangTagConn:Disconnect() wallbangTagConn = nil end
    for part, gid in pairs(AR2_wallbangSaved) do
        pcall(function()
            if part and part.Parent then part.CollisionGroupId = gid end
        end)
    end
    AR2_wallbangSaved = {}
end

-- All fire modes
AR2_originalFireModesCache = {}
AR2_allFireModesConn = nil
AR2_allFireModes_table = { "Automatic", "Semiautomatic", "Burst" }

function AR2_getEquippedInner()
    local ok, pc = pcall(function()
        return require(AR2_ReplicatedFirst:WaitForChild("Framework", 30)).Classes.Players.get()
    end)
    if not ok or not pc or not pc.Character then return nil end
    local item = pc.Character.EquippedItem
    if not item then return nil end
    return rawget(item, "__item") or item
end

function AR2_getPlayerClass()
    local ok, pc = pcall(function()
        return require(AR2_ReplicatedFirst:WaitForChild("Framework", 30)).Classes.Players.get()
    end)
    return ok and pc or nil
end

function AR2_applyToInner(inner)
    if not inner then return end
    local id = tostring(inner.Id or inner.ItemName or inner.Name or "unknown")
    if not AR2_originalFireModesCache[id] then
        AR2_originalFireModesCache[id] = inner.FireModes
    end
    inner.FireModes = AR2_allFireModes_table
    return inner
end

function AR2_applyAllFireModes(enabled)
    AR2_CFG.weapon.allFireModes = enabled
    if AR2_allFireModesConn then
        AR2_allFireModesConn:Disconnect()
        AR2_allFireModesConn = nil
    end
    if not enabled then
        local inner = AR2_getEquippedInner()
        if inner then
            local id = tostring(inner.Id or inner.ItemName or inner.Name or "unknown")
            inner.FireModes = AR2_originalFireModesCache[id] or inner.FireModes
        end
        return
    end
    local lastInner = AR2_applyToInner(AR2_getEquippedInner())
    AR2_allFireModesConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_CFG.weapon.allFireModes then return end
        local inner = AR2_getEquippedInner()
        if inner and inner ~= lastInner then lastInner = AR2_applyToInner(inner) end
    end)
end

-- Instant reload
local AR2_instantReloadEnabled = false
local AR2_instantReloadEquipConn = nil
local AR2_instantReloadOriginalMap = {}

local function AR2_instantReloadHook()
    if not AR2_instantReloadEnabled then return end
    if not AR2_CAP.getupvalue then return end
    local pc = AR2_getPlayerClass()
    if not pc or not pc.Character then return end
    local item = pc.Character.EquippedItem
    if not item then return end
    local inner = rawget(item, "__item") or item
    if not inner or type(inner.OnReload) ~= "function" then return end
    if inner.__reloadHooked then return end

    local findReloadAmmo = nil
    for i = 1, 20 do
        local ok, val = pcall(debug.getupvalue, inner.OnReload, i)
        if not ok then break end
        if type(val) == "function" then
            local infoOk, info = pcall(debug.getinfo, val)
            if infoOk and info and info.name == "findReloadAmmo" then findReloadAmmo = val break end
        end
    end
    if not findReloadAmmo then return end

    local originalOnReload = inner.OnReload
    AR2_instantReloadOriginalMap[inner] = originalOnReload

    local Network = nil
    local netOk, netResult = pcall(function()
        return require(AR2_ReplicatedFirst:WaitForChild("Framework", 30)).Libraries.Network
    end)
    if netOk then Network = netResult end

    inner.OnReload = function(gun, charClass, itemData, ...)
        if AR2_instantReloadEnabled then
            local AmmoSelection = findReloadAmmo(gun, charClass, itemData)
            if AmmoSelection then
                if Network then
                    Network:Send('Character Reload Firearm Initiated', gun.Id, AmmoSelection.Id)
                    if gun.FireConfig and gun.FireConfig.InternalMag then
                        for i = 1, gun.FireConfig.InternalMagSize do
                            Network:Send('Character Reload Firearm Committed', gun.Id, AmmoSelection.Id)
                        end
                    else
                        Network:Send('Character Reload Firearm Committed', gun.Id, AmmoSelection.Id)
                    end
                    Network:Send('Character Reload Firearm Clear', gun.Id, AmmoSelection.Id)
                end
            end
            return true
        end
        return originalOnReload(gun, charClass, itemData, ...)
    end

    inner.__reloadHooked = true
end

local function AR2_instantReloadOnEquip()
    if AR2_instantReloadEquipConn then
        AR2_instantReloadEquipConn:Disconnect()
        AR2_instantReloadEquipConn = nil
    end
    local pc = AR2_getPlayerClass()
    if not pc or not pc.Character then return end
    AR2_instantReloadEquipConn = pc.Character.EquipmentChanged:Connect(function()
        task.wait(0.1)
        if AR2_instantReloadEnabled then AR2_instantReloadHook() end
        if AR2_CFG.misc.personalGunOn then
            AR2_setPersonalGun(true, AR2_CFG.misc.personalGunMode, AR2_CFG.misc.personalGunHue, AR2_CFG.misc.personalGunGray, AR2_CFG.misc.personalGunMaterial, AR2_CFG.misc.personalGunRainbow)
        end
        if AR2_CFG.misc.hideGun then AR2_toggleHideGun(true) end
    end)
end

AR2_reloadWatchConn = nil
AR2_reloadWatchLast = 0

function setInstantReloadEnabled(enabled)
    AR2_instantReloadEnabled = enabled
    AR2_CFG.weapon.instantReload = enabled
    if enabled then
        AR2_instantReloadHook()
        AR2_instantReloadOnEquip()
        if not AR2_reloadWatchConn then
            AR2_reloadWatchConn = AR2_RunService.Heartbeat:Connect(function()
                if not AR2_instantReloadEnabled then
                    AR2_reloadWatchConn:Disconnect()
                    AR2_reloadWatchConn = nil
                    return
                end
                local now = os.clock()
                if now - AR2_reloadWatchLast < 4 then return end
                AR2_reloadWatchLast = now
                local pc = AR2_getPlayerClass()
                local actualChar = AR2_LocalPlayer.Character
                if pc and pc.Character ~= actualChar then return end
                local inner = AR2_getEquippedInner()
                if inner and not inner.__reloadHooked then AR2_instantReloadHook() end
            end)
        end
    else
        if AR2_reloadWatchConn then
            AR2_reloadWatchConn:Disconnect()
            AR2_reloadWatchConn = nil
        end
        AR2_reloadWatchLast = 0
        for weapon, origFunc in pairs(AR2_instantReloadOriginalMap) do
            if weapon and type(weapon.OnReload) == "function" then
                weapon.OnReload = origFunc
                weapon.__reloadHooked = nil
            end
        end
        table.clear(AR2_instantReloadOriginalMap)
        if AR2_instantReloadEquipConn then
            AR2_instantReloadEquipConn:Disconnect()
            AR2_instantReloadEquipConn = nil
        end
    end
end

local function AR2_reloadRespawnSetup(newChar)
    task.spawn(function()
        local deadline = os.clock() + 15
        while os.clock() < deadline do
            if not AR2_instantReloadEnabled then return end
            local hum = newChar:FindFirstChildWhichIsA("Humanoid")
            if hum and hum.Health > 0 and newChar:FindFirstChild("HumanoidRootPart") then
                task.wait(2)
                if not AR2_instantReloadEnabled then return end
                AR2_instantReloadHook()
                AR2_instantReloadOnEquip()
                return
            end
            task.wait(0.25)
        end
        if AR2_instantReloadEnabled then
            AR2_instantReloadHook()
            AR2_instantReloadOnEquip()
        end
    end)
end

AR2_LocalPlayer.CharacterAdded:Connect(function(newChar)
    task.wait(0.5)
    if AR2_instantReloadEnabled then AR2_reloadRespawnSetup(newChar) end
    if AR2_CFG.misc.personalCharOn then
        AR2_setPersonalCharacter(true, AR2_CFG.misc.personalCharMode, AR2_CFG.misc.personalCharHue, AR2_CFG.misc.personalCharGray, AR2_CFG.misc.personalCharMaterial, AR2_CFG.misc.personalCharRainbow)
    end
    if AR2_CFG.misc.personalGunOn then
        AR2_setPersonalGun(true, AR2_CFG.misc.personalGunMode, AR2_CFG.misc.personalGunHue, AR2_CFG.misc.personalGunGray, AR2_CFG.misc.personalGunMaterial, AR2_CFG.misc.personalGunRainbow)
    end
    AR2_refreshHideState()
    if AR2_CFG.misc.hideGun then AR2_toggleHideGun(true) end
    if AR2_CFG.misc.cosUnlock then AR2_cosSetEnabled(true) end
end)

if AR2_LocalPlayer.Character then
    task.wait(1)
    if AR2_instantReloadEnabled then AR2_reloadRespawnSetup(AR2_LocalPlayer.Character) end
end

-- Vehicle equip
AR2_vehEquipEnabled      = false
AR2_vehEquipPC           = nil
AR2_vehEquipOrig         = nil
AR2_vehEquipInVeh        = false
AR2_vehEquipBulletHooked = false
AR2_vehEquipOrigFire     = nil

do
    local RS = AR2_ReplicatedStorage

    local function _getPC()
        local ok, pc = pcall(function()
            return require(RS.Client.Classes.Players).get()
        end)
        return ok and pc or nil
    end

    local function _withWritable(t, fn)
        if type(t) ~= "table" then return end
        local wasReadonly = false
        pcall(function()
            if isreadonly then wasReadonly = isreadonly(t) end
        end)
        pcall(function() setreadonly(t, false) end)
        pcall(fn)
        if wasReadonly then
            pcall(function() setreadonly(t, true) end)
        end
    end

    AR2_vehEquipPC = _getPC()

    AR2_RunService.Heartbeat:Connect(function()
        pcall(function()
            local pc = AR2_vehEquipPC
            if not pc then return end
            local char = pc.Character
            local flag = char and char.IsVehicleDriver
            AR2_vehEquipInVeh = flag == true
        end)
    end)

    local okCh, Chars = pcall(function() return require(RS.Client.Classes.Characters) end)
    if okCh and Chars then AR2_vehEquipOrig = Chars.Equip end

    local TIMING = {
        "NextEquipTime","LastEquipTime","EquipDelay","NextHotbarSwitchTime",
        "UseDelay","CooldownTime","EquipCooldown","NextEquipValid","EquipReadyAt",
        "SwitchTime","NextSwitchTime","HotbarSwitchTime","EquipTime","SelectTime",
    }
    local VEH_KEYS = { "IsVehicleDriver","Sitting","Mounting","Dismounting" }

    local function _zeroTiming(item)
        if not item then return end
        _withWritable(item, function()
            for _, f in ipairs(TIMING) do rawset(item, f, 0) end
        end)
        local inner = rawget(item, "__item")
        if type(inner) == "table" then
            _withWritable(inner, function()
                for _, f in ipairs(TIMING) do rawset(inner, f, 0) end
            end)
        end
    end

    local function _customVehEquip(self, item, ...)
        _zeroTiming(item)
        if not AR2_vehEquipOrig then return end
        if not (AR2_vehEquipEnabled and AR2_vehEquipInVeh) then
            return AR2_vehEquipOrig(self, item, ...)
        end

        local saved = {}
        _withWritable(self, function()
            for _, key in ipairs(VEH_KEYS) do
                saved[key] = rawget(self, key)
                rawset(self, key, false)
            end
            saved.isItemUsable = rawget(self, "isItemUsable")
            rawset(self, "isItemUsable", function() return true end)
        end)

        local ok, result = pcall(AR2_vehEquipOrig, self, item, ...)

        _withWritable(self, function()
            for _, key in ipairs(VEH_KEYS) do rawset(self, key, saved[key]) end
            rawset(self, "isItemUsable", saved.isItemUsable)
        end)

        return ok and result or nil
    end

    local function _patch(char)
        if not char or not AR2_vehEquipOrig then return false end
        if not AR2_CAP.getrawmetatable then return false end
        local ok = pcall(function()
            local mt = getrawmetatable(char)
            if not mt then return false end
            local oldIndex = rawget(mt, "__index")
            _withWritable(mt, function()
                rawset(mt, "__index", AR2_newcclosure(function(self, key)
                    if AR2_vehEquipEnabled and key == "Equip" then
                        return _customVehEquip
                    end
                    if type(oldIndex) == "function" then return oldIndex(self, key)
                    elseif oldIndex ~= nil then return oldIndex[key] end
                    return nil
                end))
            end)
            return true
        end)
        return ok
    end

    local function _hookBullets()
        if AR2_vehEquipBulletHooked then return end
        local ok, Bullets = pcall(function() return require(RS.Client.Libraries.Bullets) end)
        if not ok or not Bullets or not Bullets.Fire then return end
        AR2_vehEquipOrigFire = Bullets.Fire

        _withWritable(Bullets, function()
            rawset(Bullets, "Fire", function(self, pc2, wi, wd, origin, dir, sid)
                if AR2_vehEquipEnabled and wd then
                    _withWritable(wd, function()
                        wd.CanFire = true wd.CanFireInVehicles = true
                        wd.InVehicle = false wd.VehicleFireLocked = false
                    end)
                    local inner = rawget(wd, "__item")
                    if type(inner) == "table" then
                        _withWritable(inner, function()
                            inner.CanFire = true inner.CanFireInVehicles = true
                            inner.InVehicle = false inner.VehicleFireLocked = false
                        end)
                    end
                end
                return AR2_vehEquipOrigFire(self, pc2, wi, wd, origin, dir, sid)
            end)
        end)

        AR2_vehEquipBulletHooked = true
    end

    if AR2_vehEquipPC and AR2_vehEquipPC.Character then
        _patch(AR2_vehEquipPC.Character)
    end
    _hookBullets()

    AR2_LocalPlayer.CharacterAdded:Connect(function()
        task.wait(1)
        local pc4 = _getPC()
        if pc4 then
            AR2_vehEquipPC = pc4
            local ok6, C = pcall(function() return require(RS.Client.Classes.Characters) end)
            if ok6 and C and C.Equip then AR2_vehEquipOrig = C.Equip end
            if pc4.Character then _patch(pc4.Character) end
        end
    end)
end

function AR2_setVehicleEquip(enabled)
    AR2_vehEquipEnabled = enabled
    AR2_CFG.weapon.vehicleEquip = enabled
end

-- Sun rays / ambient
AR2_sunRaysSaved = {}

function AR2_setRemoveSunRays(enabled)
    AR2_CFG.visuals.removeSunRays = enabled
    AR2_sunRaysSaved = AR2_sunRaysSaved or {}
    for _, child in ipairs(AR2_Lighting:GetChildren()) do
        if child:IsA("SunRaysEffect") then
            if AR2_sunRaysSaved[child] == nil then
                AR2_sunRaysSaved[child] = child.Enabled
            end
            child.Enabled = not enabled
        end
    end
end

AR2_ambientSaved = nil
AR2_ambientConn  = nil

function AR2_setCustomAmbient(enabled)
    AR2_CFG.visuals.customAmbient = enabled
    if AR2_ambientSaved == nil then
        AR2_ambientSaved = {
            Ambient        = AR2_Lighting.Ambient,
            OutdoorAmbient = AR2_Lighting.OutdoorAmbient,
        }
    end
    if enabled then
        AR2_Lighting.Ambient        = AR2_CFG.visuals.ambientIndoor
        AR2_Lighting.OutdoorAmbient = AR2_CFG.visuals.ambientOutdoor
        if not AR2_ambientConn then
            AR2_ambientConn = AR2_RunService.RenderStepped:Connect(function()
                if not AR2_CFG.visuals.customAmbient then return end
                AR2_Lighting.Ambient        = AR2_CFG.visuals.ambientIndoor
                AR2_Lighting.OutdoorAmbient = AR2_CFG.visuals.ambientOutdoor
            end)
        end
    else
        if AR2_ambientConn then
            AR2_ambientConn:Disconnect()
            AR2_ambientConn = nil
        end
        if AR2_ambientSaved then
            AR2_Lighting.Ambient        = AR2_ambientSaved.Ambient
            AR2_Lighting.OutdoorAmbient = AR2_ambientSaved.OutdoorAmbient
        end
    end
end

-- Skybox / clouds / color correction
local AR2_skyboxCache = {}
function AR2_applySkybox(skyboxName)
    AR2_CFG.visuals.skybox = skyboxName
    if not AR2_skyboxCache.original then
        AR2_skyboxCache.original = {
            Ambient = AR2_Lighting.Ambient, OutdoorAmbient = AR2_Lighting.OutdoorAmbient,
            FogColor = AR2_Lighting.FogColor, FogStart = AR2_Lighting.FogStart,
            FogEnd = AR2_Lighting.FogEnd, ClockTime = AR2_Lighting.ClockTime,
        }
    end

    if AR2_customSky and AR2_customSky.Parent then
        pcall(function() AR2_customSky:Destroy() end)
    end
    for _, child in ipairs(AR2_Lighting:GetChildren()) do
        if child:IsA("Sky") and child:GetAttribute("ar2_sky") then
            pcall(function() child:Destroy() end)
        end
    end
    AR2_customSky = nil
    if skyboxName == "Default" then
        AR2_Lighting.Ambient = AR2_skyboxCache.original.Ambient
        AR2_Lighting.OutdoorAmbient = AR2_skyboxCache.original.OutdoorAmbient
        AR2_Lighting.FogColor = AR2_skyboxCache.original.FogColor
        AR2_Lighting.FogStart = AR2_skyboxCache.original.FogStart
        AR2_Lighting.FogEnd = AR2_skyboxCache.original.FogEnd
        AR2_Lighting.ClockTime = AR2_skyboxCache.original.ClockTime
    else
        local skyboxes = {
            ["Space"] = { Bk = "rbxassetid://159454299", Dn = "rbxassetid://159454299", Ft = "rbxassetid://159454299", Lf = "rbxassetid://159454299", Rt = "rbxassetid://159454299", Up = "rbxassetid://159454299" },
            ["Galaxy"] = { Bk = "http://www.roblox.com/asset/?id=149397692", Dn = "http://www.roblox.com/asset/?id=149397686", Ft = "http://www.roblox.com/asset/?id=149397697", Lf = "http://www.roblox.com/asset/?id=149397684", Rt = "http://www.roblox.com/asset/?id=149397688", Up = "http://www.roblox.com/asset/?id=149397702" },
            ["Galaxy 2"] = { Bk = "http://www.roblox.com/asset/?id=155441936", Dn = "http://www.roblox.com/asset/?id=155441802", Ft = "http://www.roblox.com/asset/?id=155441818", Lf = "http://www.roblox.com/asset/?id=155441777", Rt = "http://www.roblox.com/asset/?id=155441874", Up = "http://www.roblox.com/asset/?id=155441905" },
            ["Galaxy 3"] = { Bk = "rbxassetid://135908594667929", Dn = "rbxassetid://139584143501514", Ft = "rbxassetid://92947876187368", Lf = "rbxassetid://72493016739936", Rt = "rbxassetid://81731245279712", Up = "rbxassetid://88174897344210" },
            ["Saturne"] = { Bk = "rbxassetid://1898724755", Dn = "rbxassetid://1898727189", Ft = "rbxassetid://1898722814", Lf = "rbxassetid://1898729298", Rt = "rbxassetid://1898741025", Up = "rbxassetid://1898736761" },
            ["Neptune"] = { Bk = "rbxassetid://218955819", Dn = "rbxassetid://218953419", Ft = "rbxassetid://218954524", Lf = "rbxassetid://218958493", Rt = "rbxassetid://218957134", Up = "rbxassetid://218950090" },
            ["Redshift"] = { Bk = "rbxassetid://401664839", Dn = "rbxassetid://401664862", Ft = "rbxassetid://401664960", Lf = "rbxassetid://401664881", Rt = "rbxassetid://401664901", Up = "rbxassetid://401664936" },
            ["Pink Daylights"] = { Bk = "rbxassetid://11555017034", Dn = "rbxassetid://11555013415", Ft = "rbxassetid://11555010145", Lf = "rbxassetid://11555006545", Rt = "rbxassetid://11555000712", Up = "rbxassetid://11554996247" },
            ["Purple Night"] = { Bk = "rbxassetid://17279854976", Dn = "rbxassetid://17279856318", Ft = "rbxassetid://17279858447", Lf = "rbxassetid://17279860360", Rt = "rbxassetid://17279862234", Up = "rbxassetid://17279864507" },
            ["Gray Night"] = { Bk = "rbxassetid://1618912481", Dn = "rbxassetid://1618913943", Ft = "rbxassetid://1618913244", Lf = "rbxassetid://1618912849", Rt = "rbxassetid://1618911568", Up = "rbxassetid://1618913654" },
            ["Anime Sky"] = { Bk = "rbxassetid://18351376859", Dn = "rbxassetid://18351374919", Ft = "rbxassetid://18351376800", Lf = "rbxassetid://18351376469", Rt = "rbxassetid://18351376457", Up = "rbxassetid://18351377189" }
        }
        local data = skyboxes[skyboxName]
        if data then
            AR2_Lighting.Ambient = Color3.new(0.5, 0.5, 0.5)
            AR2_Lighting.OutdoorAmbient = Color3.new(0.8, 0.8, 0.8)
            AR2_Lighting.FogColor = Color3.new(0.5, 0.5, 0.5)
            AR2_Lighting.FogStart = 0
            AR2_Lighting.FogEnd = 1000
            AR2_Lighting.ClockTime = 0
            local sky = Instance.new("Sky")
            sky.Name = skyboxName
            sky:SetAttribute("ar2_sky", true)
            sky.SkyboxBk = data.Bk
            sky.SkyboxDn = data.Dn
            sky.SkyboxFt = data.Ft
            sky.SkyboxLf = data.Lf
            sky.SkyboxRt = data.Rt
            sky.SkyboxUp = data.Up
            sky.Parent = AR2_Lighting
            AR2_customSky = sky
        end
    end
end

AR2_cloudsInstance = nil
AR2_cloudsConn     = nil
AR2_cloudsState    = {
    enabled = false,
    modify  = false,
    color   = Color3.fromRGB(255, 255, 255),
    cover   = 0.5,
    density = 0.5,
}

do
    local c = AR2_Workspace:FindFirstChildOfClass("Clouds")
    if not c and AR2_Workspace.Terrain then
        c = AR2_Workspace.Terrain:FindFirstChildOfClass("Clouds")
    end
    if not c then
        c = AR2_Lighting:FindFirstChildOfClass("Clouds")
    end
    AR2_cloudsInstance = c
    if c then
        AR2_cloudsState.color   = c.Color
        AR2_cloudsState.cover   = c.Cover
        AR2_cloudsState.density = c.Density
        AR2_cloudsState.enabled = c.Enabled
    end
end

function AR2_setClouds(enabled, modify, color, cover, density)
    AR2_CFG.visuals.cloudsEnabled = enabled
    AR2_CFG.visuals.modifyClouds  = modify
    if color   then AR2_CFG.visuals.cloudsColor   = color   end
    if cover   then AR2_CFG.visuals.cloudsCover   = cover   end
    if density then AR2_CFG.visuals.cloudsDensity = density end

    AR2_cloudsState.enabled = enabled and true or false
    AR2_cloudsState.modify  = modify  and true or false
    if color   then AR2_cloudsState.color   = color   end
    if cover   then AR2_cloudsState.cover   = cover   end
    if density then AR2_cloudsState.density = density end

    local c = AR2_cloudsInstance
    if not c or not c.Parent then
        c = AR2_Workspace:FindFirstChildOfClass("Clouds")
        if not c and AR2_Workspace.Terrain then
            c = AR2_Workspace.Terrain:FindFirstChildOfClass("Clouds")
        end
        if not c then c = AR2_Lighting:FindFirstChildOfClass("Clouds") end
        AR2_cloudsInstance = c
    end
    if not c then return end

    pcall(function() c.Enabled = AR2_cloudsState.enabled end)

    if AR2_cloudsState.modify then
        pcall(function()
            c.Color   = AR2_cloudsState.color
            c.Cover   = AR2_cloudsState.cover
            c.Density = AR2_cloudsState.density
        end)
    end

    if not AR2_cloudsConn then
        AR2_cloudsConn = AR2_RunService.RenderStepped:Connect(function()
            local ci = AR2_cloudsInstance
            if not ci or not ci.Parent then return end
            if not AR2_cloudsState.modify then return end
            pcall(function()
                ci.Color   = AR2_cloudsState.color
                ci.Cover   = AR2_cloudsState.cover
                ci.Density = AR2_cloudsState.density
            end)
        end)
    end
end

local AR2_ccInst = nil
function AR2_setColorCorrection(enabled, saturation, contrast, brightness)
    AR2_CFG.visuals.colorCorrectionEnabled = enabled
    if saturation then AR2_CFG.visuals.ccSaturation = saturation end
    if contrast then AR2_CFG.visuals.ccContrast = contrast end
    if brightness then AR2_CFG.visuals.ccBrightness = brightness end
    if not enabled then
        if AR2_ccInst then AR2_ccInst:Destroy() AR2_ccInst = nil end
        return
    end
    if not AR2_ccInst then
        AR2_ccInst = Instance.new("ColorCorrectionEffect")
        AR2_ccInst.Parent = AR2_Lighting
    end
    AR2_ccInst.Saturation = AR2_CFG.visuals.ccSaturation
    AR2_ccInst.Contrast   = AR2_CFG.visuals.ccContrast * 2
    AR2_ccInst.Brightness = AR2_CFG.visuals.ccBrightness - 1
    AR2_ccInst.Enabled = true
end

-- ============================================================
-- 45. SELF BACKTRACK / JUMP CIRCLE / SHADOWS / ZOMBIE CIRCLE / ANTI-AIM
-- ============================================================
local AR2_selfBacktrackConn = nil
local AR2_selfBacktrackVisConn = nil
local AR2_backtrackHistory = {}
local AR2_backtrackMaxHistory = 200
local AR2_backtrackParts = {
    "Head","UpperTorso","LowerTorso","LeftUpperArm","LeftLowerArm","LeftHand",
    "RightUpperArm","RightLowerArm","RightHand","LeftUpperLeg","LeftLowerLeg",
    "LeftFoot","RightUpperLeg","RightLowerLeg","RightFoot"
}

local function AR2_createFakeBacktrack(char, cf)
    local old = workspace:FindFirstChild("tetescr_selfBacktrack")
    if old then old:Destroy() end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local model = Instance.new("Model")
    model.Name = "tetescr_selfBacktrack"
    for _, name in ipairs(AR2_backtrackParts) do
        local orig = char:FindFirstChild(name)
        if orig and orig:IsA("BasePart") then
            local clone = orig:Clone()
            clone.Anchored = true
            clone.CanCollide = false
            clone.CanQuery = false
            clone.CanTouch = false
            clone.CastShadow = false
            clone.Massless = true
            clone.Transparency = AR2_CFG.misc.selfBacktrack.transparency or 0.4
            clone.Color = AR2_CFG.misc.selfBacktrack.color or Color3.fromRGB(255,255,255)
            clone.Material = AR2_CFG.misc.selfBacktrack.material or Enum.Material.ForceField
            clone.Name = name
            for _, child in ipairs(clone:GetChildren()) do
                if not child:IsA("SpecialMesh") and not child:IsA("DataModelMesh") then child:Destroy() end
            end
            local offset = root.CFrame:Inverse() * orig.CFrame
            clone.CFrame = cf * offset
            clone.Parent = model
        end
    end
    model.Parent = workspace
end

function AR2_setSelfBacktrack(enabled)
    AR2_CFG.misc.selfBacktrack.enabled = enabled
    if AR2_selfBacktrackConn then AR2_selfBacktrackConn:Disconnect() AR2_selfBacktrackConn = nil end
    if AR2_selfBacktrackVisConn then AR2_selfBacktrackVisConn:Disconnect() AR2_selfBacktrackVisConn = nil end
    AR2_backtrackHistory = {}
    if not enabled then return end
    AR2_selfBacktrackConn = AR2_RunService.Heartbeat:Connect(function()
        local char = AR2_LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local now = tick()
        table.insert(AR2_backtrackHistory, { time = now, cframe = root.CFrame })
        while #AR2_backtrackHistory > AR2_backtrackMaxHistory do table.remove(AR2_backtrackHistory, 1) end
        local targetTime = now - AR2_CFG.misc.selfBacktrack.delay
        local best, bestDiff = nil, math.huge
        for _, rec in ipairs(AR2_backtrackHistory) do
            local diff = math.abs(rec.time - targetTime)
            if diff < bestDiff then bestDiff = diff best = rec end
        end
        if best then pcall(AR2_createFakeBacktrack, char, best.cframe) end
    end)
    AR2_selfBacktrackVisConn = AR2_RunService.RenderStepped:Connect(function()
        local model = workspace:FindFirstChild("tetescr_selfBacktrack")
        if not model then return end
        local camPos = AR2_Camera.CFrame.Position
        local torso = model:FindFirstChild("UpperTorso") or model:FindFirstChild("LowerTorso")
        if not torso then return end
        local hide = (torso.Position - camPos).Magnitude < 5
        for _, part in ipairs(model:GetChildren()) do
            if part:IsA("BasePart") then part.LocalTransparencyModifier = hide and 1 or 0 end
        end
    end)
end

local AR2_jumpCircleConn = nil

local function AR2_createJumpCircleEffect(pos, color, startRadius, endRadius, duration, icon)
    task.spawn(function()
        local part = Instance.new("Part")
        part.Name = "tetescr_jumpCircle"
        part.Shape = Enum.PartType.Block
        part.Material = Enum.Material.ForceField
        part.Transparency = 1
        part.Color = color
        part.Size = Vector3.new(startRadius, 0.05, startRadius)
        part.Anchored = true
        part.CanCollide = false
        part.CastShadow = false
        local char = AR2_LocalPlayer.Character
        local rp = RaycastParams.new()
        if char then rp.FilterDescendantsInstances = {char} end
        rp.FilterType = Enum.RaycastFilterType.Exclude
        local hit = workspace:Raycast(pos, Vector3.new(0,-15,0), rp)
        part.Position = hit and (hit.Position + Vector3.new(0,0.1,0)) or (pos - Vector3.new(0,3,0))
        local decal = Instance.new("Decal")
        decal.Texture = icon
        decal.Face = Enum.NormalId.Top
        decal.Transparency = 0.1
        decal.Color3 = color
        decal.Parent = part
        part.Parent = workspace
        local expandTween = AR2_TweenService:Create(part, TweenInfo.new(duration, Enum.EasingStyle.Circular, Enum.EasingDirection.Out), { Size = Vector3.new(endRadius, 0.05, endRadius) })
        local fadeTween = AR2_TweenService:Create(decal, TweenInfo.new(duration * 0.8, Enum.EasingStyle.Quart, Enum.EasingDirection.In), { Transparency = 1 })
        expandTween:Play()
        fadeTween:Play()
        expandTween.Completed:Once(function() part:Destroy() end)
    end)
end

function AR2_setJumpCircle(enabled)
    AR2_CFG.misc.jumpCircle.enabled = enabled
    if AR2_jumpCircleConn then AR2_jumpCircleConn:Disconnect() AR2_jumpCircleConn = nil end
    if not enabled then return end
    local function onChar(char)
        local hum = char:WaitForChild("Humanoid")
        hum.StateChanged:Connect(function(_, state)
            if state == Enum.HumanoidStateType.Jumping then
                local root = char:FindFirstChild("HumanoidRootPart")
                if root then
                    AR2_createJumpCircleEffect(root.Position, AR2_CFG.misc.jumpCircle.color, AR2_CFG.misc.jumpCircle.startRadius, AR2_CFG.misc.jumpCircle.endRadius, AR2_CFG.misc.jumpCircle.duration, "rbxassetid://89391029290549")
                end
            end
        end)
    end
    AR2_jumpCircleConn = AR2_LocalPlayer.CharacterAdded:Connect(onChar)
    if AR2_LocalPlayer.Character then onChar(AR2_LocalPlayer.Character) end
end

function AR2_setRemoveShadows(enabled)
    AR2_CFG.misc.removeShadows = enabled
    AR2_Lighting.GlobalShadows = not enabled
end

local AR2_zombieCircleConn = nil
function AR2_setZombieCircle(enabled)
    AR2_CFG.misc.zombieCircle.enabled = enabled
    if AR2_zombieCircleConn then AR2_zombieCircleConn:Disconnect() AR2_zombieCircleConn = nil end
    if not enabled then return end
    AR2_zombieCircleConn = AR2_RunService.Heartbeat:Connect(function()
        local root = AR2_LocalPlayer.Character and AR2_LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if not root or not AR2_Zombies then return end
        local zombies = AR2_Zombies:GetChildren()
        if #zombies == 0 then return end
        local angleStep = math.pi * 2 / #zombies
        for i, zombie in ipairs(zombies) do
            local zr = zombie:FindFirstChild("HumanoidRootPart")
            if zr then
                local angle = tick() * AR2_CFG.misc.zombieCircle.speed + i * angleStep
                local offset = Vector3.new(math.cos(angle) * AR2_CFG.misc.zombieCircle.distance, 0, math.sin(angle) * AR2_CFG.misc.zombieCircle.distance)
                zr.CFrame = CFrame.new(root.Position + offset)
                zr.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end)
end

local AR2_antiAimConn   = nil
local AR2_antiAimCenter = nil
local AR2_antiAimAngle  = 0

function AR2_startAntiAim()
    if AR2_antiAimConn then return end
    local root = AR2_tpRoot()
    if not root then return end
    AR2_antiAimCenter = root.Position
    AR2_antiAimAngle  = 0
    AR2_antiAimConn = AR2_RunService.Heartbeat:Connect(function(dt)
        if not AR2_CFG.misc.antiAim.enabled then AR2_stopAntiAim() return end
        local r = AR2_tpRoot()
        if not r then return end
        local c = AR2_CFG.misc.antiAim
        local offset
        if c.mode == "Jitter" then
            local angle = math.random() * math.pi * 2
            local dist  = math.random() * c.radius
            offset = Vector3.new(math.cos(angle) * dist, 0, math.sin(angle) * dist)
        else
            AR2_antiAimAngle = (AR2_antiAimAngle + dt * c.speed * (math.pi * 2)) % (math.pi * 2)
            offset = Vector3.new(math.cos(AR2_antiAimAngle) * c.radius, 0, math.sin(AR2_antiAimAngle) * c.radius)
        end
        r.CFrame                   = CFrame.new(AR2_antiAimCenter + offset)
        r.AssemblyLinearVelocity   = Vector3.zero
        r.AssemblyAngularVelocity  = Vector3.zero
    end)
end

function AR2_stopAntiAim()
    AR2_CFG.misc.antiAim.enabled = false
    if AR2_antiAimConn then AR2_antiAimConn:Disconnect() AR2_antiAimConn = nil end
    local r = AR2_tpRoot()
    if r and AR2_antiAimCenter then
        r.CFrame = CFrame.new(AR2_antiAimCenter)
        r.AssemblyLinearVelocity  = Vector3.zero
        r.AssemblyAngularVelocity = Vector3.zero
    end
    AR2_antiAimCenter = nil
end

function AR2_toggleAntiAim(enabled)
    AR2_CFG.misc.antiAim.enabled = enabled
    if enabled then AR2_startAntiAim() else AR2_stopAntiAim() end
end

-- ============================================================
-- 46. INSTANT INTERACT / PLAYER JESUS / HIT SOUND / ALWAYS SUPPRESSED
-- ============================================================
local AR2_instantInteractEnabled = false
local AR2_instantInteractOriginal = nil

function AR2_setInstantInteract(enabled)
    AR2_instantInteractEnabled = enabled
    AR2_CFG.misc.instantInteract = enabled
    if enabled then
        local Fw = require(AR2_ReplicatedFirst:WaitForChild("Framework", 30))
        local Interactables = Fw.Classes.Interactables
        if Interactables and Interactables.GetInteractPromptData then
            if not AR2_instantInteractOriginal then
                AR2_instantInteractOriginal = Interactables.GetInteractPromptData
            end
            Interactables.GetInteractPromptData = function(...)
                local data = AR2_instantInteractOriginal(...)
                if data and data.Actions then
                    for _, action in ipairs(data.Actions) do action.Time = 0 end
                end
                return data
            end
        end
    else
        if AR2_instantInteractOriginal then
            local Fw = require(AR2_ReplicatedFirst:WaitForChild("Framework", 30))
            local Interactables = Fw.Classes.Interactables
            if Interactables then Interactables.GetInteractPromptData = AR2_instantInteractOriginal end
            AR2_instantInteractOriginal = nil
        end
    end
end

local AR2_playerJesusEnabled = false
function AR2_setPlayerJesus(enabled)
    AR2_playerJesusEnabled = enabled
    AR2_CFG.misc.playerJesus = enabled
    local waterFolder = AR2_Workspace:FindFirstChild("Map") and AR2_Workspace.Map:FindFirstChild("Water") or AR2_Workspace:FindFirstChild("Water")
    if not waterFolder then return end
    for _, part in ipairs(waterFolder:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            if enabled then
                if part:HasTag("Swim Surface") then
                    part:RemoveTag("Swim Surface")
                    part:AddTag("Player Jesus")
                end
            else
                if part:HasTag("Player Jesus") then
                    part:RemoveTag("Player Jesus")
                    part:AddTag("Swim Surface")
                end
            end
        end
    end
end

AR2_hitSoundId      = nil
AR2_hitSoundOrig    = nil
AR2_hitSoundKeep    = nil
AR2_hitSoundApplied = nil

local function AR2_impactSounds()
    local rs = game:GetService("ReplicatedStorage")
    local assets = rs:FindFirstChild("Assets")
    if not assets then return nil, nil end
    local sounds = assets:FindFirstChild("Sounds")
    if not sounds then return nil, nil end
    local impact = sounds:FindFirstChild("Impact")
    if not impact then return nil, nil end
    local head = impact:FindFirstChild("Headshot")
    local body = impact:FindFirstChild("Bodyshot")
    return head, body
end

local function AR2_resolveHitSoundId(input)
    if not input or input == "" or input == "None" then return nil end
    if string.find(input, "rbxassetid://", 1, true)
    or string.find(input, "rbxasset://",   1, true) then
        return input
    end
    if tonumber(input) then
        return "rbxassetid://" .. input
    end
    local gca = getcustomasset or getsynasset
    if typeof(gca) ~= "function" then
        warn("[hitmarker] no getcustomasset — can't load local file: " .. input)
        return nil
    end
    if typeof(isfile) == "function" then
        local exists = false
        pcall(function() exists = isfile(input) end)
        if not exists then
            warn("[hitmarker] file not found: " .. input)
            return nil
        end
    end
    local ok, result = pcall(gca, input)
    if not ok or not result then
        warn("[hitmarker] getcustomasset failed for " .. input .. ": " .. tostring(result))
        return nil
    end
    return result
end

function AR2_hitSoundLoad(input)
    if AR2_hitSoundKeep then
        pcall(function() AR2_hitSoundKeep:Disconnect() end)
        AR2_hitSoundKeep = nil
    end
    local head, body = AR2_impactSounds()
    if not head and not body then
        warn("[hitmarker] Impact sounds not found in ReplicatedStorage")
        return
    end
    if AR2_hitSoundOrig == nil then
        AR2_hitSoundOrig = {
            head = head and head.SoundId or nil,
            body = body and body.SoundId or nil,
        }
    end
    if not input or input == "" then
        local id   = AR2_CFG.hitmarker.sound
        local path = AR2_CFG.hitmarker.soundPath
        if path and path ~= "" then
            input = path
        elseif id and id ~= "" and id ~= "None" and id ~= "Custom..." then
            input = id
        else
            input = "None"
        end
    end
    if not input or input == "" or input == "None" then
        AR2_hitSoundApplied = nil
        AR2_hitSoundId      = nil
        if head and AR2_hitSoundOrig.head then head.SoundId = AR2_hitSoundOrig.head end
        if body and AR2_hitSoundOrig.body then body.SoundId = AR2_hitSoundOrig.body end
        return
    end
    local resolved = AR2_resolveHitSoundId(input)
    if not resolved then return end
    local vol = math.clamp((AR2_CFG.misc.hitSoundVolume or 70) / 100, 0, 10)
    if head then
        head.SoundId = resolved
        pcall(function() head.Volume = vol end)
    end
    if body then
        body.SoundId = resolved
        pcall(function() body.Volume = vol end)
    end
    AR2_hitSoundApplied = resolved
    AR2_hitSoundId      = input
    AR2_hitSoundKeep = AR2_RunService.RenderStepped:Connect(function()
        local h, b = AR2_impactSounds()
        local want = AR2_hitSoundApplied
        if not want then return end
        local v = math.clamp((AR2_CFG.misc.hitSoundVolume or 70) / 100, 0, 10)
        if h then
            if h.SoundId ~= want then h.SoundId = want end
            if h.Volume  ~= v    then pcall(function() h.Volume = v end) end
        end
        if b then
            if b.SoundId ~= want then b.SoundId = want end
            if b.Volume  ~= v    then pcall(function() b.Volume = v end) end
        end
    end)
end

function AR2_hitSoundReload()
    AR2_hitSoundLoad(AR2_hitSoundId)
end

local AR2_alwaysSuppressedOrig = nil
local function AR2_applyAlwaysSuppressedHook()
    if AR2_alwaysSuppressedOrig then return end
    local fwOk, fw = pcall(function()
        return require(AR2_ReplicatedFirst:WaitForChild("Framework", 30))
    end)
    if not fwOk or not fw then return end
    local bullets = fw.Libraries and fw.Libraries.Bullets
    if not bullets or type(bullets.Fire) ~= "function" then return end

    AR2_alwaysSuppressedOrig = bullets.Fire

    pcall(function() if AR2_CAP.setreadonly then setreadonly(bullets, false) end end)
    pcall(function()
        rawset(bullets, "Fire", function(...)
            local args = {...}
            if AR2_CFG.weapon.alwaysSuppressed and args[4] then
                pcall(function() args[4].SuppressedByDefault = true end)
            end
            return AR2_alwaysSuppressedOrig(unpack(args))
        end)
    end)
    pcall(function() if AR2_CAP.setreadonly then setreadonly(bullets, true) end end)
end

function AR2_setAlwaysSuppressed(enabled)
    AR2_CFG.weapon.alwaysSuppressed = enabled
    pcall(AR2_applyAlwaysSuppressedHook)
end

task.spawn(function()
    AR2_ReplicatedFirst:WaitForChild("Framework", 30)
    AR2_applyAlwaysSuppressedHook()
end)

-- ============================================================
-- 47. COSMÉTICOS
-- ============================================================
AR2_cosEnabled       = false
AR2_cosRendering     = false
AR2_cosEquipped      = {}
AR2_cosSlot          = 0
AR2_cosHooked        = {}
AR2_cosWatchConn     = nil
AR2_cosEnforceConn   = nil

local AR2_wantedShirt    = nil
local AR2_wantedPants    = nil
local AR2_origShirt      = nil
local AR2_origPants      = nil
local AR2_defaultTemplates = {}

local function AR2_cosGetTemplates()
    local char = AR2_LocalPlayer.Character
    if not char then return nil, nil end
    local s = char:FindFirstChildOfClass("Shirt")
    local p = char:FindFirstChildOfClass("Pants")
    return s and s.ShirtTemplate or nil,
           p and p.PantsTemplate or nil
end

local function AR2_cosScanDefaults()
    AR2_defaultTemplates = {}
    local counts = {}
    local gui    = AR2_LocalPlayer:FindFirstChild("PlayerGui")
    if not gui then return end
    for _, d in ipairs(gui:GetDescendants()) do
        if d:IsA("ViewportFrame") then
            for _, wm in ipairs(d:GetChildren()) do
                if wm:IsA("WorldModel") then
                    for _, vm in ipairs(wm:GetChildren()) do
                        if vm:IsA("Model") then
                            for _, obj in ipairs(vm:GetChildren()) do
                                local tt = nil
                                if obj:IsA("Shirt") then tt = obj.ShirtTemplate
                                elseif obj:IsA("Pants") then tt = obj.PantsTemplate end
                                if tt and tt ~= "" then
                                    counts[tt] = (counts[tt] or 0) + 1
                                end
                            end
                        end
                    end
                end
            end
        end
    end
    for tt, n in pairs(counts) do
        if n >= 3 then AR2_defaultTemplates[tt] = true end
    end
end

local function AR2_cosStartEnforce()
    if AR2_cosEnforceConn then AR2_cosEnforceConn:Disconnect() end
    AR2_cosEnforceConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_cosRendering then return end
        local char = AR2_LocalPlayer.Character
        if not char then return end
        if AR2_wantedShirt then
            local s = char:FindFirstChildOfClass("Shirt")
            if not s then s = Instance.new("Shirt") s.Parent = char end
            if s.ShirtTemplate ~= AR2_wantedShirt then
                pcall(function() s.ShirtTemplate = AR2_wantedShirt end)
            end
        end
        if AR2_wantedPants then
            local p = char:FindFirstChildOfClass("Pants")
            if not p then p = Instance.new("Pants") p.Parent = char end
            if p.PantsTemplate ~= AR2_wantedPants then
                pcall(function() p.PantsTemplate = AR2_wantedPants end)
            end
        end
    end)
end

local AR2_COS_BODY = {
    HumanoidRootPart=true, Head=true,
    UpperTorso=true, LowerTorso=true, Torso=true,
    ["Left Arm"]=true, ["Right Arm"]=true,
    ["Left Leg"]=true, ["Right Leg"]=true,
    LeftUpperArm=true, LeftLowerArm=true, LeftHand=true,
    RightUpperArm=true, RightLowerArm=true, RightHand=true,
    LeftUpperLeg=true, LeftLowerLeg=true, LeftFoot=true,
    RightUpperLeg=true, RightLowerLeg=true, RightFoot=true,
}

local function AR2_cosFindVM(root)
    for _, d in ipairs(root:GetDescendants()) do
        if d:IsA("ViewportFrame") then
            for _, wm in ipairs(d:GetChildren()) do
                if wm:IsA("WorldModel") then
                    for _, vm in ipairs(wm:GetChildren()) do
                        if vm:IsA("Model") and (
                            vm:FindFirstChild("HumanoidRootPart") or
                            vm:FindFirstChild("UpperTorso") or
                            vm:FindFirstChild("Torso")
                        ) then return vm end
                    end
                end
            end
        end
    end
end

local function AR2_cosFindNear(el)
    local node = el
    for _ = 1, 6 do
        if not node then break end
        local vm = AR2_cosFindVM(node)
        if vm then return vm end
        node = node.Parent
    end
end

local function AR2_cosNearestBody(vm, pos)
    local best, bestD = nil, math.huge
    for _, p in ipairs(vm:GetDescendants()) do
        if p:IsA("BasePart") and AR2_COS_BODY[p.Name] then
            local d = (p.Position - pos).Magnitude
            if d < bestD then best = p bestD = d end
        end
    end
    return best
end

local function AR2_cosIsWeapon(p, vmRoot)
    local node = p.Parent
    while node and node ~= vmRoot do
        if node:IsA("Tool") then return true end
        node = node.Parent
    end
    return false
end

local function AR2_cosClonePart(p, anchor, nbCF)
    local ok, clone = pcall(function() return p:Clone() end)
    if not ok or not clone then return nil end
    for _, c in ipairs(clone:GetChildren()) do
        if c:IsA("Motor6D") or c:IsA("Weld") or c:IsA("WeldConstraint")
        or c:IsA("Script") or c:IsA("LocalScript")
        or c:IsA("BodyVelocity") or c:IsA("Humanoid") then
            c:Destroy()
        end
    end
    clone.Anchored   = true  clone.CanCollide = false
    clone.CanTouch   = false clone.CanQuery   = false
    clone.CastShadow = false clone.Massless   = true
    clone.Parent     = AR2_Camera
    pcall(function() AR2_CollectionService:AddTag(clone, "Bullets Penetrate") end)
    return clone, { anchor = anchor, localCF = nbCF:Inverse() * p.CFrame }
end

local function AR2_cosAttach(vm)
    local char = AR2_LocalPlayer.Character
    if not char then return end

    local curShirt, curPants = AR2_cosGetTemplates()
    local vmShirt = vm:FindFirstChildOfClass("Shirt")
    local vmPants = vm:FindFirstChildOfClass("Pants")
    local vmST    = vmShirt and vmShirt.ShirtTemplate or ""
    local vmPT    = vmPants and vmPants.PantsTemplate or ""

    if vmST ~= "" and vmST ~= curShirt and not AR2_defaultTemplates[vmST] then
        AR2_wantedShirt = vmST
    end
    if vmPT ~= "" and vmPT ~= curPants and not AR2_defaultTemplates[vmPT] then
        AR2_wantedPants = vmPT
    end

    local parts   = {}
    local bpCount = 0
    local hrp     = char:FindFirstChild("HumanoidRootPart")

    for _, p in ipairs(vm:GetDescendants()) do
        if p:IsA("BasePart") and not AR2_COS_BODY[p.Name] then
            if AR2_cosIsWeapon(p, vm) then continue end
            local nb = AR2_cosNearestBody(vm, p.Position)
            local clone, data = AR2_cosClonePart(
                p,
                nb and nb.Name or "HumanoidRootPart",
                nb and nb.CFrame or CFrame.new()
            )
            if clone then
                local anchor = char:FindFirstChild(data.anchor) or hrp
                if anchor then clone.CFrame = anchor.CFrame * data.localCF end
                parts[clone] = data
                bpCount = bpCount + 1
            end
        end
    end
    if bpCount > 0 then
        AR2_cosSlot = AR2_cosSlot + 1
        AR2_cosEquipped[AR2_cosSlot] = parts
    end
end

AR2_RunService.RenderStepped:Connect(function()
    if not AR2_cosRendering then return end
    local char = AR2_LocalPlayer.Character
    local hrp  = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for slot, ptbl in pairs(AR2_cosEquipped) do
        local empty = true
        for part, data in pairs(ptbl) do
            if part and part.Parent then
                local anchor = char:FindFirstChild(data.anchor) or hrp
                part.CFrame  = anchor.CFrame * data.localCF
                empty = false
            else ptbl[part] = nil end
        end
        if empty then AR2_cosEquipped[slot] = nil end
    end
end)

function AR2_cosRemoveAll()
    for slot, ptbl in pairs(AR2_cosEquipped) do
        for part in pairs(ptbl) do
            pcall(function() if part and part.Parent then part:Destroy() end end)
        end
        AR2_cosEquipped[slot] = nil
    end
    AR2_wantedShirt = AR2_origShirt
    AR2_wantedPants = AR2_origPants
    if not AR2_cosEnabled then
        AR2_cosRendering = false
        if AR2_cosEnforceConn then AR2_cosEnforceConn:Disconnect() AR2_cosEnforceConn = nil end
        AR2_wantedShirt, AR2_wantedPants = nil, nil
        AR2_origShirt,   AR2_origPants   = nil, nil
    end
end

local function AR2_cosHookBtn(btn)
    if AR2_cosHooked[btn] then return end
    AR2_cosHooked[btn] = btn.MouseButton1Click:Connect(function()
        if not AR2_cosEnabled then return end
        task.wait(0.15)
        local vm = AR2_cosFindNear(btn)
        if vm then AR2_cosAttach(vm) end
    end)
end

local function AR2_cosScan()
    local gui = AR2_LocalPlayer:FindFirstChild("PlayerGui")
    if not gui then return end
    for _, d in ipairs(gui:GetDescendants()) do
        if d:IsA("ImageButton") or d:IsA("TextButton") then
            pcall(AR2_cosHookBtn, d)
        end
    end
end

function AR2_cosSetEnabled(on)
    AR2_cosEnabled = on
    AR2_CFG.misc.cosUnlock = on

    if on then
        AR2_cosRendering = true
        AR2_origShirt, AR2_origPants = AR2_cosGetTemplates()
        if not AR2_wantedShirt then AR2_wantedShirt = AR2_origShirt end
        if not AR2_wantedPants then AR2_wantedPants = AR2_origPants end
        AR2_cosScanDefaults()
        AR2_cosStartEnforce()
        AR2_cosScan()
        local gui = AR2_LocalPlayer:FindFirstChild("PlayerGui")
        if gui and not AR2_cosWatchConn then
            AR2_cosWatchConn = gui.DescendantAdded:Connect(function(d)
                if not AR2_cosEnabled then return end
                if d:IsA("ImageButton") or d:IsA("TextButton") then
                    task.wait(0.1) pcall(AR2_cosHookBtn, d)
                end
            end)
        end
    else
        if AR2_cosWatchConn then AR2_cosWatchConn:Disconnect() AR2_cosWatchConn = nil end
        for _, conn in pairs(AR2_cosHooked) do pcall(function() conn:Disconnect() end) end
        AR2_cosHooked = {}
    end
end

-- ============================================================
-- 48. MORPH
-- ============================================================
AR2_morph = AR2_morph or { model = nil }

local function AR2_morphToast(text)
    pcall(function()
        game:GetService("StarterGui"):SetCore("SendNotification",
            { Title = "Tetescr hub", Text = tostring(text), Duration = 4 })
    end)
end

function AR2_removeMorph()
    if AR2_morph.model then
        pcall(function() AR2_morph.model:Destroy() end)
        AR2_morph.model = nil
    end
    pcall(AR2_refreshHideState)
end

function AR2_applyMorph()
    AR2_removeMorph()

    local id = tostring(AR2_CFG.misc.morphId or ""):gsub("%D", "")
    if id == "" then AR2_morphToast("Enter a model ID first") return false end

    local char = AR2_LocalPlayer.Character
    local hrp  = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then AR2_morphToast("No character") return false end

    local anchor = char:FindFirstChild("UpperTorso")
                or char:FindFirstChild("Torso")
                or char:FindFirstChild("LowerTorso")
                or hrp

    local objs
    local ok = pcall(function() objs = game:GetObjects("rbxassetid://" .. id) end)
    if not ok or type(objs) ~= "table" or #objs == 0 then
        objs = nil
        pcall(function()
            local m = game:GetService("InsertService"):LoadAsset(tonumber(id))
            if m then objs = m:GetChildren() end
        end)
    end
    if type(objs) ~= "table" or #objs == 0 then
        AR2_morphToast("Couldn't load model " .. id .. " (private or invalid?)")
        return false
    end

    local model = objs[1]
    for _, o in ipairs(objs) do if o:IsA("Model") then model = o break end end
    model.Name = "oh_morph"
    model.Parent = workspace
    pcall(function()
        if model:IsA("Model") then model:PivotTo(hrp.CFrame)
        elseif model:IsA("BasePart") then model.CFrame = hrp.CFrame end
    end)

    pcall(function()
        for _, d in ipairs(model:GetDescendants()) do
            if d:IsA("Script") or d:IsA("LocalScript") or d:IsA("Humanoid")
            or d:IsA("Weld") or d:IsA("WeldConstraint") or d:IsA("Motor6D")
            or d:IsA("Snap") or d:IsA("ManualWeld") then d:Destroy() end
        end
    end)

    local parts = {}
    if model:IsA("Model") then
        for _, d in ipairs(model:GetDescendants()) do if d:IsA("BasePart") then parts[#parts+1] = d end end
    elseif model:IsA("BasePart") then parts[1] = model end
    if #parts == 0 then
        pcall(function() model:Destroy() end)
        AR2_morphToast("That asset has no parts to weld")
        return false
    end

    for _, p in ipairs(parts) do
        pcall(function()
            p.Anchored = false p.CanCollide = false p.Massless = true
            local w = Instance.new("Weld")
            w.Part0 = anchor w.Part1 = p
            w.C0 = anchor.CFrame:ToObjectSpace(p.CFrame)
            w.Parent = p
        end)
    end

    AR2_morph.model = model

    pcall(AR2_refreshHideState)
    AR2_morphToast("Morph applied (" .. #parts .. " parts)")
    return true
end

AR2_LocalPlayer.CharacterAdded:Connect(function()
    if AR2_morph.model then
        task.wait(1.5)
        pcall(AR2_applyMorph)
    end
    task.wait(0.1)
    pcall(AR2_refreshHideState)
end)

-- ============================================================
-- 49. FOV ZOOM / DIST ZOOM / 3P ADS
-- ============================================================
local AR2_fovZoomConn = nil
local AR2_fovZoomOriginal = nil
AR2_zoomFOV = AR2_CFG.misc.zoomFOV

function AR2_toggleFOVZoom(enabled)
    AR2_CFG.misc.fovZoom = enabled
    if AR2_fovZoomConn then AR2_fovZoomConn:Disconnect() AR2_fovZoomConn = nil end
    if AR2_fovZoomOriginal then
        AR2_Camera.FieldOfView = AR2_fovZoomOriginal
        AR2_fovZoomOriginal = nil
    end
    if not enabled then return end
    AR2_fovZoomConn = AR2_RunService.RenderStepped:Connect(function()
        local holding = AR2_UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2)
        local cam = AR2_Camera
        if holding then
            AR2_fovZoomOriginal = AR2_fovZoomOriginal or cam.FieldOfView
            cam.FieldOfView = AR2_zoomFOV
        elseif AR2_fovZoomOriginal then
            cam.FieldOfView = AR2_fovZoomOriginal
            AR2_fovZoomOriginal = nil
        end
    end)
end

local AR2_distZoomConn = nil
local AR2_distZoomActive = AR2_CFG.misc.distZoomActive or false
local AR2_normalDist = AR2_CFG.misc.normalDist
local AR2_zoomedDist = AR2_CFG.misc.zoomedDist
local targetDist = AR2_normalDist

function AR2_toggleDistZoomEnabled(enabled)
    AR2_CFG.misc.distZoomEnabled = enabled
    if AR2_distZoomConn then AR2_distZoomConn:Disconnect() AR2_distZoomConn = nil end
    if not enabled then
        pcall(function()
            local humanoid = AR2_LocalPlayer.Character and AR2_LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            workspace.CurrentCamera.CameraSubject = humanoid
            workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
        end)
        return
    end
    AR2_distZoomConn = AR2_RunService.RenderStepped:Connect(function()
        local char = AR2_LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        local cam = workspace.CurrentCamera
        if not (root and cam) then return end
        local dist = targetDist
        local look = cam.CFrame.LookVector
        local desiredPos = root.Position + Vector3.new(0, 2, 0) - look * dist
        cam.CFrame = CFrame.new(desiredPos, root.Position + Vector3.new(0, 2, 0))
    end)
end

function AR2_toggleDistanceZoom(active)
    AR2_CFG.misc.distZoomActive = active
    AR2_distZoomActive = active
    targetDist = active and AR2_zoomedDist or AR2_normalDist
    if active and not AR2_CFG.misc.distZoomEnabled then AR2_toggleDistZoomEnabled(true) end
end

function AR2_setNormalDistance(value)
    AR2_normalDist = value
    AR2_CFG.misc.normalDist = value
    if not AR2_distZoomActive then targetDist = value end
end

function AR2_setZoomedDistance(value)
    AR2_zoomedDist = value
    AR2_CFG.misc.zoomedDist = value
    if AR2_distZoomActive then targetDist = value end
end

AR2_3pAds = {
    active     = false,
    normalFOV  = 70,
    currentPos = nil,
    fovConn    = nil,
    geoParams  = RaycastParams.new(),
}
AR2_3pAds.geoParams.FilterType = Enum.RaycastFilterType.Exclude

function AR2_3pAds.safePos(charPos, targetPos, char)
    pcall(function()
        AR2_3pAds.geoParams.FilterDescendantsInstances = { char, AR2_Camera }
    end)
    local dir  = targetPos - charPos
    local dist = dir.Magnitude
    if dist < 0.1 then return targetPos end
    local hit = AR2_Workspace:Raycast(charPos, dir.Unit * (dist + 0.5), AR2_3pAds.geoParams)
    if hit then
        return charPos + dir.Unit * math.max(0.5, hit.Distance - 0.5)
    end
    return targetPos
end

function AR2_3pAds.start()
    local c = AR2_CFG.misc.thirdPersonADS
    if not c.enabled or AR2_3pAds.active then return end
    if AR2_CFG.misc.fly or AR2_flyActive or AR2_CFG.car.fly or AR2_carFlyActive then return end
    local char = AR2_LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    AR2_3pAds.active     = true
    AR2_3pAds.normalFOV  = AR2_Camera.FieldOfView
    AR2_3pAds.currentPos = AR2_Camera.CFrame.Position

    AR2_RunService:BindToRenderStep(
        "AR2_3PADS",
        Enum.RenderPriority.Camera.Value + 1,
        function()
            if not AR2_3pAds.active then return end
            local ch = AR2_LocalPlayer.Character
            local r  = ch and ch:FindFirstChild("HumanoidRootPart")
            if not r then return end

            local ar2Look = AR2_Camera.CFrame.LookVector

            pcall(function()
                local anim = ch:FindFirstChild("Animator")
                if anim then
                    local fp = anim:FindFirstChild("FirstPerson")
                    if fp and fp.Value then fp.Value = false end
                end
            end)

            AR2_Camera.FieldOfView = AR2_Camera.FieldOfView
                + (c.adsFOV - AR2_Camera.FieldOfView) * 0.15

            local flatLook = Vector3.new(ar2Look.X, 0, ar2Look.Z)
            if flatLook.Magnitude < 0.01 then flatLook = Vector3.new(0, 0, -1) end
            flatLook = flatLook.Unit

            local right   = flatLook:Cross(Vector3.new(0, 1, 0)).Unit
            local charPos = r.Position + Vector3.new(0, 1, 0)

            local rawTarget = charPos
                + right * c.offsetRight
                + Vector3.new(0, c.offsetUp, 0)
                - flatLook * c.offsetBack

            local safeTarget = AR2_3pAds.safePos(charPos, rawTarget, ch)

            if not AR2_3pAds.currentPos then AR2_3pAds.currentPos = safeTarget end
            AR2_3pAds.currentPos = AR2_3pAds.currentPos:Lerp(safeTarget, c.smooth)

            local aimPoint = charPos + ar2Look * 500
            AR2_Camera.CFrame = CFrame.new(AR2_3pAds.currentPos, aimPoint)
        end
    )
end

function AR2_3pAds.stop()
    if not AR2_3pAds.active then return end
    AR2_3pAds.active     = false
    AR2_3pAds.currentPos = nil
    AR2_RunService:UnbindFromRenderStep("AR2_3PADS")

    local target = AR2_3pAds.normalFOV
    if AR2_3pAds.fovConn then AR2_3pAds.fovConn:Disconnect() end
    AR2_3pAds.fovConn = AR2_RunService.RenderStepped:Connect(function()
        local diff = target - AR2_Camera.FieldOfView
        if math.abs(diff) < 0.3 then
            AR2_Camera.FieldOfView = target
            AR2_3pAds.fovConn:Disconnect()
            AR2_3pAds.fovConn = nil
        else
            AR2_Camera.FieldOfView = AR2_Camera.FieldOfView + diff * 0.2
        end
    end)
end

function AR2_set3pAds(enabled)
    AR2_CFG.misc.thirdPersonADS.enabled = enabled
    if not enabled and AR2_3pAds.active then AR2_3pAds.stop() end
end

AR2_UserInputService.InputBegan:Connect(function(input, gpe)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        if AR2_CFG.misc.thirdPersonADS.enabled then AR2_3pAds.start() end
    end
end)

AR2_UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        if AR2_3pAds.active then AR2_3pAds.stop() end
    end
end)

AR2_LocalPlayer.CharacterAdded:Connect(function()
    if AR2_3pAds.active then AR2_3pAds.stop() end
end)

-- Auto reload
local AR2_autoReloadEnabled = false
local AR2_autoReloadConn = nil
local AR2_autoReloadLast = 0

function AR2_setAutoReload(enabled)
    AR2_autoReloadEnabled = enabled
    AR2_CFG.weapon.autoReload = enabled
    if AR2_autoReloadConn then AR2_autoReloadConn:Disconnect() AR2_autoReloadConn = nil end
    if not enabled then return end
    AR2_autoReloadConn = AR2_RunService.Heartbeat:Connect(function()
        if not AR2_autoReloadEnabled then return end
        local now = os.clock()
        if now - AR2_autoReloadLast < 1 then return end
        AR2_autoReloadLast = now
        pcall(function()
            local inner = AR2_getEquippedInner()
            if inner and inner.Attachments and inner.Attachments.Ammo then
                local ammo = inner.Attachments.Ammo.__item.WorkingAmount
                if ammo == 0 then
                    local VIM = game:GetService("VirtualInputManager")
                    VIM:SendKeyEvent(true, Enum.KeyCode.R, false, game)
                    task.wait(0.05)
                    VIM:SendKeyEvent(false, Enum.KeyCode.R, false, game)
                end
            end
        end)
    end)
end

-- ============================================================
-- 50. TUNG TUNG TUNG
-- ============================================================
local TAN = Color3.fromRGB(210, 163, 100)
local BODY_PARTS = {
    "Head","UpperTorso","LowerTorso","LeftUpperArm","LeftLowerArm","LeftHand",
    "RightUpperArm","RightLowerArm","RightHand","LeftUpperLeg","LeftLowerLeg",
    "LeftFoot","RightUpperLeg","RightLowerLeg","RightFoot"
}
local originalParts = {}
local tungGlobalConns = {}
local tungEnabled = false
local tungGlobal = false

local function clearTung(char)
    if not char then return end
    for _, d in ipairs(char:GetDescendants()) do
        if typeof(d) == "Instance" and d.Name:sub(1,4) == "Tung" then
            pcall(function() d:Destroy() end)
        end
    end
end

local function weld(p0,p1)
    local w = Instance.new("WeldConstraint") w.Part0 = p0 w.Part1 = p1 w.Parent = p1
end

local function makePart(props)
    local p = Instance.new("Part")
    p.Name = props.name or "TungPart"
    p.Size = props.size or Vector3.new(1,1,1)
    p.Color = props.color or TAN
    p.Material = props.material or Enum.Material.SmoothPlastic
    p.Transparency = props.transparency or 0
    p.Anchored = false
    p.CanCollide = false
    p.Massless = true
    p.CastShadow = false
    if props.shape then p.Shape = props.shape end
    p.Parent = props.parent
    p.CFrame = props.cframe or CFrame.new()
    return p
end

local function applyTungTung(char)
    if not char then return end
    clearTung(char)
    for _, name in ipairs(BODY_PARTS) do
        local part = char:FindFirstChild(name)
        if part and part:IsA("BasePart") then
            if char == AR2_LocalPlayer.Character and not originalParts[part] then
                originalParts[part] = { Color = part.Color, Material = part.Material, Transparency = part.Transparency }
            end
            part.Material = Enum.Material.ForceField
            part.Color = TAN
            part.Transparency = 0.5
        end
    end
    local head = char:FindFirstChild("Head")
    local rightHand = char:FindFirstChild("RightHand") or char:FindFirstChild("Right Arm") or char:FindFirstChild("RightLowerArm")
    if head then
        local function makeEye(name, xOffset)
            local eye = makePart({ name=name, shape=Enum.PartType.Ball, size=Vector3.new(0.30,0.30,0.30), color=Color3.fromRGB(255,255,255), material=Enum.Material.SmoothPlastic, transparency=0, parent=char, cframe=head.CFrame*CFrame.new(xOffset,0.22,-0.53) })
            local pupil = makePart({ name=name.."Pupil", shape=Enum.PartType.Ball, size=Vector3.new(0.13,0.13,0.13), color=Color3.fromRGB(0,0,0), material=Enum.Material.SmoothPlastic, transparency=0, parent=char, cframe=eye.CFrame*CFrame.new(0,0,-0.12) })
            weld(head,eye) weld(eye,pupil)
        end
        makeEye("TungLeftEye",-0.22) makeEye("TungRightEye",0.22)
    end
    if rightHand then
        local handleLen, handleW, barrelLen, barrelW, tapeH = 2.2,0.18,1.0,0.55,0.30
        local handle = makePart({ name="TungBatHandle", size=Vector3.new(handleW,handleLen,handleW), color=Color3.fromRGB(88,52,12), material=Enum.Material.Wood, parent=char, cframe=rightHand.CFrame*CFrame.new(0,-(handleLen/2),0) })
        local barrel = makePart({ name="TungBatBarrel", size=Vector3.new(barrelW,barrelLen,barrelW), color=Color3.fromRGB(88,52,12), material=Enum.Material.Wood, parent=char, cframe=handle.CFrame*CFrame.new(0,-(handleLen/2+barrelLen/2),0) })
        local tape = makePart({ name="TungBatTape", size=Vector3.new(handleW+0.06,tapeH,handleW+0.06), color=Color3.fromRGB(20,20,20), material=Enum.Material.SmoothPlastic, parent=char, cframe=handle.CFrame*CFrame.new(0,handleLen/2-tapeH/2-0.1,0) })
        weld(rightHand,handle) weld(handle,barrel) weld(handle,tape)
    end
end

local function removeTungTung(char)
    clearTung(char)
    if not char then return end
    for part, orig in pairs(originalParts) do
        if part and part.Parent then
            part.Color = orig.Color
            part.Material = orig.Material
            part.Transparency = orig.Transparency
        end
    end
    table.clear(originalParts)
end

local function applyTungToAll(on)
    for _, conn in pairs(tungGlobalConns) do pcall(function() conn:Disconnect() end) end
    tungGlobalConns = {}
    if not on then
        for _, p in ipairs(AR2_Players:GetPlayers()) do
            if p~=AR2_LocalPlayer and p.Character then clearTung(p.Character) end
        end
        return
    end
    for _, p in ipairs(AR2_Players:GetPlayers()) do
        if p~=AR2_LocalPlayer and p.Character then applyTungTung(p.Character) end
    end
    for _, p in ipairs(AR2_Players:GetPlayers()) do
        if p~=AR2_LocalPlayer then
            tungGlobalConns[p] = p.CharacterAdded:Connect(function(char)
                task.wait(0.5)
                if tungGlobal then applyTungTung(char) end
            end)
        end
    end
    tungGlobalConns["PlayerAdded"] = AR2_Players.PlayerAdded:Connect(function(p)
        tungGlobalConns[p] = p.CharacterAdded:Connect(function(char)
            task.wait(0.5)
            if tungGlobal then applyTungTung(char) end
        end)
    end)
end

function toggleTungSelf(enabled)
    tungEnabled = enabled
    if enabled then applyTungTung(AR2_LocalPlayer.Character) else removeTungTung(AR2_LocalPlayer.Character) end
end

function toggleTungGlobal(enabled)
    tungGlobal = enabled
    applyTungToAll(enabled)
end

AR2_LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(0.5)
    if tungEnabled then applyTungTung(char) end
end)

-- ============================================================
-- 51. PERFORMANCE
-- ============================================================
local AR2_textureUpdateConn = nil
local AR2_textureLastScan = 0
local AR2_savedTextures = {}

local function AR2_scanTextures()
    for _, obj in ipairs(AR2_Workspace:GetDescendants()) do
        if (obj:IsA("Texture") or obj:IsA("Decal")) and obj.Transparency < 1 and not AR2_savedTextures[obj] then
            AR2_savedTextures[obj] = obj.Transparency
            obj.Transparency = 1
        end
    end
end

function AR2_toggleRemoveTextures(enabled)
    AR2_CFG.misc.removeTextures = enabled
    if enabled then
        AR2_scanTextures()
        if not AR2_textureUpdateConn then
            AR2_textureUpdateConn = AR2_RunService.Heartbeat:Connect(function()
                local now = os.clock()
                if now - AR2_textureLastScan >= 20 then
                    AR2_textureLastScan = now
                    AR2_scanTextures()
                end
            end)
        end
    else
        if AR2_textureUpdateConn then
            AR2_textureUpdateConn:Disconnect()
            AR2_textureUpdateConn = nil
        end
        for obj, tt in pairs(AR2_savedTextures) do
            if obj and obj.Parent then obj.Transparency = tt end
        end
        AR2_savedTextures = {}
        AR2_textureLastScan = 0
    end
end

local AR2_leaveConn = nil
local AR2_leaveSaved = {}

local function AR2_applyLeafTransparency(part)
    if not AR2_leaveSaved[part] then AR2_leaveSaved[part] = part.Transparency end
    part.Transparency = 1
end

local function AR2_scanLeaves()
    local mapElements = AR2_Workspace:FindFirstChild("Map") and AR2_Workspace.Map:FindFirstChild("Elements")
    if not mapElements then return end
    for _, part in ipairs(mapElements:GetDescendants()) do
        if part.Name == "Fronds" or part.Name == "Leaves" or part.Name == "PineLeaves" then
            AR2_applyLeafTransparency(part)
        end
    end
end

function AR2_toggleRemoveLeaves(enabled)
    AR2_CFG.misc.removeLeaves = enabled
    if enabled then
        AR2_scanLeaves()
        if not AR2_leaveConn then
            local mapElements = AR2_Workspace:FindFirstChild("Map") and AR2_Workspace.Map:FindFirstChild("Elements")
            if mapElements then
                AR2_leaveConn = mapElements.DescendantAdded:Connect(function(part)
                    if part.Name == "Fronds" or part.Name == "Leaves" or part.Name == "PineLeaves" then
                        task.wait()
                        AR2_applyLeafTransparency(part)
                    end
                end)
            end
        end
    else
        if AR2_leaveConn then
            AR2_leaveConn:Disconnect()
            AR2_leaveConn = nil
        end
        for part, orig in pairs(AR2_leaveSaved) do
            if part.Parent then part.Transparency = orig end
        end
        AR2_leaveSaved = {}
    end
end

local AR2_bushRemoved = {}

local function AR2_removeBushes()
    local mapElements = AR2_Workspace:FindFirstChild("Map") and AR2_Workspace.Map:FindFirstChild("Elements")
    if not mapElements then return end
    local function scan(parent)
        for _, child in ipairs(parent:GetChildren()) do
            if child.Name:find("Foliage") or child.Name:find("Shrub") then
                AR2_bushRemoved[child] = child.Parent
                child.Parent = nil
            else
                scan(child)
            end
        end
    end
    scan(mapElements)
end

local function AR2_restoreBushes()
    for child, parent in pairs(AR2_bushRemoved) do
        if child and parent then child.Parent = parent end
    end
    AR2_bushRemoved = {}
end

function AR2_toggleRemoveBushes(enabled)
    AR2_CFG.misc.removeBushes = enabled
    if enabled then
        AR2_removeBushes()
    else
        AR2_restoreBushes()
    end
end

-- ============================================================
-- 52. KEYBINDS
-- ============================================================
AR2_keybindActions = {}
function AR2_registerKeybind(name,fn) AR2_keybindActions[name]=fn end

local function AR2_fireKeybind(keyName)
    local fired = false
    for feature,key in pairs(AR2_CFG.keybinds) do
        if key and key ~= "None" and key ~= "" then
            local norm = AR2_normalizeKey(key)
            if norm == keyName then
                local action = AR2_keybindActions[feature]
                if action then pcall(action) fired = true end
            end
        end
    end
    if fired then
    end
end

AR2_UserInputService.InputBegan:Connect(function(input,gpe)
    if gpe then return end
    local keyName
    if input.UserInputType==Enum.UserInputType.MouseButton3 then keyName="MouseButton3" else keyName=input.KeyCode.Name end
    AR2_fireKeybind(keyName)
end)

local function AR2_registerAllKeybinds()
    AR2_registerKeybind("playerESP",function() AR2_CFG.players.on=not AR2_CFG.players.on AR2_esp.update() end)
    AR2_registerKeybind("zombieESP",function() AR2_CFG.zombies.on=not AR2_CFG.zombies.on AR2_esp.update() end)
    AR2_registerKeybind("lootESP",function() AR2_CFG.loot.on=not AR2_CFG.loot.on AR2_esp.update() end)
        AR2_registerKeybind("corpseESP", function()
        AR2_CFG.corpses.on = not AR2_CFG.corpses.on
        AR2_esp.update()
    end)
    AR2_registerKeybind("vehicleESP", function()
        AR2_CFG.vehicles.on = not AR2_CFG.vehicles.on
        AR2_esp.update()
    end)
    AR2_registerKeybind("vehicleNoclip", function()
        local on = not AR2_CFG.misc.carNoclip
        if on then
            local ok = AR2_carNoclipSetEnabled(true, true)
            if not ok then AR2_notify("Precisa estar sentado num veículo", 3) return end
            AR2_carNoclipDriveStart()
        else
            AR2_carNoclipDriveStop()
            AR2_carNoclipSetEnabled(false)
            task.spawn(function()
                task.wait(AR2_CFG.car.noclipEjectDelay or 0.15)
                pcall(AR2_ejectFromVehicle)
            end)
        end
    end)
    AR2_registerKeybind("silentAim",function() AR2_CFG.magicAim.on=not AR2_CFG.magicAim.on AR2_magicUpdate() end)
    AR2_registerKeybind("rageBot",function() AR2_rageBotSetEnabled(not AR2_CFG.rageBot.on) end)
    AR2_registerKeybind("hbe",function() AR2_hbeSetEnabled(not AR2_CFG.weapon.hbeOn) end)
    AR2_registerKeybind("noSpread",function() AR2_CFG.weapon.noSpread=not AR2_CFG.weapon.noSpread end)
    AR2_registerKeybind("instantReload",function() setInstantReloadEnabled(not AR2_CFG.weapon.instantReload) end)
    AR2_registerKeybind("allFireModes",function() AR2_applyAllFireModes(not AR2_CFG.weapon.allFireModes) end)
    AR2_registerKeybind("wallbang",function() AR2_CFG.weapon.wallbang=not AR2_CFG.weapon.wallbang if AR2_CFG.weapon.wallbang then startWallbangTagging() else stopWallbangTagging() end end)
    AR2_registerKeybind("alwaysSuppressed",function() AR2_setAlwaysSuppressed(not AR2_CFG.weapon.alwaysSuppressed) end)
    AR2_registerKeybind("vehicleEquip",function() AR2_setVehicleEquip(not AR2_CFG.weapon.vehicleEquip) end)
    AR2_registerKeybind("boatMode",function() AR2_setBoatMode(not AR2_CFG.car.boatMode) end)
    AR2_registerKeybind("removeDrag",function() AR2_setRemoveDrag(not AR2_CFG.car.removeDrag) end)
    AR2_registerKeybind("fullSteer",function() AR2_setFullSteer(not AR2_CFG.car.fullSteer) end)
    AR2_registerKeybind("antiFallStun",function() AR2_setAntiFallStun(not AR2_CFG.misc.antiFallStun) end)
    AR2_registerKeybind("hitmarker",function() AR2_toggleHitmarker(not AR2_CFG.hitmarker.enabled) end)
    AR2_registerKeybind("crosshair",function() AR2_toggleCrosshair(not AR2_CFG.crosshair.enabled) end)
    AR2_registerKeybind("tracers",function() AR2_CFG.misc.tracers=not AR2_CFG.misc.tracers AR2_tracersUpdate() end)
    AR2_registerKeybind("speedHack",function() AR2_CFG.speed.on=not AR2_CFG.speed.on if AR2_CFG.speed.on then AR2_speed.start() else AR2_speed.stop() end end)
    AR2_registerKeybind("infJump",function() AR2_CFG.misc.infJump=not AR2_CFG.misc.infJump if AR2_CFG.misc.infJump then AR2_startInfJump() else AR2_stopInfJump() end end)
    AR2_registerKeybind("spinBot",function() AR2_setSpin(not AR2_CFG.misc.spinOn) end)
    AR2_registerKeybind("noclip",function() AR2_toggleNoclip(not AR2_CFG.misc.noclip) end)
    AR2_registerKeybind("bhop",function() AR2_CFG.misc.bhop=not AR2_CFG.misc.bhop if AR2_CFG.misc.bhop then AR2_startBhop() else AR2_stopBhop() end end)
    AR2_registerKeybind("tpDash",function() AR2_CFG.misc.tpDash=not AR2_CFG.misc.tpDash if AR2_CFG.misc.tpDash then AR2_startTpDash() else AR2_stopTpDash() end end)
    AR2_registerKeybind("carTpDash",function() AR2_CFG.car.tpDash=not AR2_CFG.car.tpDash if AR2_CFG.car.tpDash then AR2_startCarDash() else AR2_stopCarDash() end end)
    AR2_registerKeybind("carFly",function() AR2_carFlySetEnabled(not AR2_CFG.car.fly) end)
    AR2_registerKeybind("fly",function() AR2_setFly(not AR2_CFG.misc.fly) end)
    AR2_registerKeybind("flyRecover",function() AR2_resetFallState() end)
    AR2_registerKeybind("freezeZombies",function() AR2_CFG.misc.freeze=not AR2_CFG.misc.freeze if AR2_CFG.misc.freeze then AR2_freeze.start() else AR2_freeze.stop() end end)
    AR2_registerKeybind("fullbright",function() AR2_CFG.visuals.fullbright=not AR2_CFG.visuals.fullbright AR2_visuals.apply() end)
    AR2_registerKeybind("antiAim",function() AR2_toggleAntiAim(not AR2_CFG.misc.antiAim.enabled) end)
    AR2_registerKeybind("zombieCircle",function() AR2_setZombieCircle(not AR2_CFG.misc.zombieCircle.enabled) end)
    AR2_registerKeybind("jumpCircle",function() AR2_setJumpCircle(not AR2_CFG.misc.jumpCircle.enabled) end)
    AR2_registerKeybind("selfBacktrack",function() AR2_setSelfBacktrack(not AR2_CFG.misc.selfBacktrack.enabled) end)
    AR2_registerKeybind("instantInteract",function() AR2_setInstantInteract(not AR2_CFG.misc.instantInteract) end)
    AR2_registerKeybind("playerJesus",function() AR2_setPlayerJesus(not AR2_CFG.misc.playerJesus) end)
    AR2_registerKeybind("thirdPersonADS",function() AR2_set3pAds(not AR2_CFG.misc.thirdPersonADS.enabled) if AR2_refreshUI then AR2_refreshUI() end end)
    AR2_registerKeybind("resetConfig",function() AR2_resetConfig() end)
end

AR2_registerAllKeybinds()

-- ============================================================
-- 53. BOTÃO FLUTUANTE (abre/fecha UI) + KEYBIND DE TOGGLE
-- ============================================================
AR2_minimizeGui = nil
AR2_minimizeStroke = nil
AR2_uiMinimized = false
AR2_uiHidden = false
AR2_minimizeDragConn = nil
AR2_minimizeUpConn = nil

function AR2_buildMinimizeButton()
    if AR2_minimizeGui and AR2_minimizeGui.Parent then return AR2_minimizeGui end
    local host = (typeof(gethui) == "function" and gethui()) or game:GetService("CoreGui")
    AR2_minimizeGui = Instance.new("ScreenGui")
    AR2_minimizeGui.Name = "tetescr_restore"
    AR2_minimizeGui.ResetOnSpawn = false
    AR2_minimizeGui.IgnoreGuiInset = true
    AR2_minimizeGui.DisplayOrder = 1000000
    AR2_minimizeGui.Enabled = true
    AR2_minimizeGui.Parent = host

    local btn = Instance.new("TextButton")
    btn.Name = "restore"
    btn.Size = UDim2.fromOffset(120, 30)
    btn.Position = UDim2.fromOffset(14, 14)
    btn.BackgroundColor3 = Color3.fromRGB(16, 18, 24)
    btn.BackgroundTransparency = 0.08
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.Text = "Tetescr"
    btn.TextColor3 = Color3.fromRGB(235, 235, 245)
    btn.Parent = AR2_minimizeGui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = btn

    AR2_minimizeStroke = Instance.new("UIStroke")
    AR2_minimizeStroke.Color = ACCENT_DEFAULT
    AR2_minimizeStroke.Thickness = 1
    AR2_minimizeStroke.Transparency = 0.2
    AR2_minimizeStroke.Parent = btn

    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(28, 32, 42)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(16, 18, 24)
    end)

    local dragging, moved, dragStart, startPos = false, false, nil, nil

    btn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging, moved = true, false
            dragStart = input.Position
            startPos = btn.Position
        end
    end)

    AR2_minimizeDragConn = AR2_UserInputService.InputChanged:Connect(function(input)
        if not dragging then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            local delta = input.Position - dragStart
            if math.abs(delta.X) > 4 or math.abs(delta.Y) > 4 then moved = true end
            btn.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    AR2_minimizeUpConn = AR2_UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            if dragging and not moved then
                AR2_toggleUI()
            end
            dragging = false
        end
    end)

    return AR2_minimizeGui
end

function AR2_toggleUI()
    local NLx = getgenv().TetescrHUB_NL
    if not NLx or not NLx.ScreenGui then return end
    AR2_uiHidden = not AR2_uiHidden
    NLx.ScreenGui.Enabled = not AR2_uiHidden
    if AR2_minimizeStroke then
        AR2_minimizeStroke.Color = AR2_uiHidden and Color3.fromRGB(80, 255, 140) or ACCENT_DEFAULT
    end
    local btn = AR2_minimizeGui and AR2_minimizeGui:FindFirstChild("restore")
    if btn then
        btn.Text = "Tetescr"
    end
end

-- ============================================================
-- 54. UNLOAD
-- ============================================================
function AR2_unload()
    AR2_esp.destroy()
    AR2_aim.destroy()
    AR2_magicUpdate()
    AR2_tracersStop()
    AR2_freeze.destroy()
    AR2_visuals.destroy()
    AR2_carMods.destroy()
    AR2_radar.destroy()
    AR2_speed.stop()
    pcall(function() AR2_setSpin(false) end)
    pcall(AR2_rageBotSetEnabled, false)
    AR2_toggleFOVZoom(false)
    AR2_toggleDistZoomEnabled(false)
    AR2_toggleDistanceZoom(false)
    AR2_set3pAds(false)
    AR2_stopNoclip()
    AR2_stopInfJump()
    AR2_stopBhop()
    AR2_stopTpDash()
    AR2_stopCarDash()
    AR2_carFlyStop()
    AR2_carNoclipStop()
    if AR2_carNoclipTP then AR2_carNoclipTP.stop() end
    AR2_stopFly()
    AR2_toggleRemoveTextures(false)
    AR2_toggleRemoveLeaves(false)
    AR2_toggleRemoveBushes(false)
    AR2_toggleHidePlayer(false)
    AR2_toggleHideGun(false)
    AR2_removeMorph()
    stopWallbangTagging()
    AR2_applyAllFireModes(false)
    setInstantReloadEnabled(false)
    AR2_setPersonalCharacter(false)
    AR2_setPersonalGun(false)
    if AR2_CFG.visuals.skybox ~= "Default" then AR2_applySkybox("Default") end
    AR2_toggleHitmarker(false)
    AR2_toggleCrosshair(false)
    AR2_toggleRespawnTP(false)
    AR2_hbeSetEnabled(false)
    AR2_cosSetEnabled(false)
    AR2_cosRemoveAll()
    AR2_setAutoReload(false)
    AR2_setInstantInteract(false)
    AR2_setPlayerJesus(false)
    AR2_setSelfBacktrack(false)
    AR2_setJumpCircle(false)
    AR2_setRemoveShadows(false)
    AR2_setColorCorrection(false)
    AR2_toggleWorldwide(false)
    AR2_stopAntiAim()
    AR2_setZombieCircle(false)
    AR2_setAlwaysSuppressed(false)
    AR2_setBoatMode(false)
    AR2_setRemoveDrag(false)
    AR2_setFullSteer(false)
    AR2_setAntiFallStun(false)
    AR2_setVehicleEquip(false)
    AR2_setRemoveDoors(false)
    AR2_setRemoveSunRays(false)
    AR2_setCustomAmbient(false)
    AR2_hitSoundLoad("")
    if AR2_mapConn then AR2_mapConn:Disconnect() end
    if AR2_mapGui then AR2_mapGui:Destroy() end
    if AR2_minimapGui then AR2_minimapGui:Destroy() end
    if AR2_minimizeGui then AR2_minimizeGui:Destroy() AR2_minimizeGui = nil end
    if AR2_flyHudGui then AR2_flyHudGui:Destroy() AR2_flyHudGui = nil end
end
getgenv().__tetescr_hub_unload = AR2_unload

if getgenv then getgenv().TetescrHUB_Loaded = nil end

-- ============================================================
-- FIM DO BLOCO D
-- ============================================================
print("[Tetescr hub] Bloco D carregado — TP + noclip + fly + car + visuals + misc prontos")
--[[
    Tetescr hub — Bloco E (parte 1/2)
    componentes + patchSection + janela + esconde sidebar + chips horizontais
]]

-- ============================================================
-- 55. COMPONENTES REESCRITOS DO ZERO
-- ============================================================
local function makeCheckRow(sectionRoot, cfg)
    cfg = cfg or {}
    local name     = cfg.Name or "Option"
    local key      = cfg.TransKey
    local default  = cfg.Default and true or false
local callback = cfg.Callback or function() end
local setter   = cfg.Setter
local accent   = cfg.Accent or ACCENT_DEFAULT

    local row = Instance.new("Frame")
    row.Name = "row"
    row.BackgroundColor3 = ROW_BG
    row.BackgroundTransparency = 1
    row.BorderSizePixel = 0
    row.Size = UDim2.new(1, -10, 0, 28)
    row.Parent = sectionRoot

    local rowBg = Instance.new("Frame")
    rowBg.Name = "rowBg"
    rowBg.BackgroundColor3 = ROW_BG_HOVER
    rowBg.BackgroundTransparency = 1
    rowBg.BorderSizePixel = 0
    rowBg.Size = UDim2.fromScale(1, 1)
    rowBg.ZIndex = row.ZIndex + 1
    rowBg.Parent = row

    local box = Instance.new("Frame")
    box.Name = "box"
    box.BackgroundColor3 = default and accent or BOX_OFF
    box.BorderSizePixel = 0
    box.AnchorPoint = Vector2.new(0, 0.5)
    box.Position = UDim2.new(0, 12, 0.5, 0)
    box.Size = UDim2.fromOffset(14, 14)
    box.ZIndex = row.ZIndex + 10
    box.Parent = row

    local chk = Instance.new("TextLabel")
    chk.Name = "check"
    chk.BackgroundTransparency = 1
    chk.Size = UDim2.fromScale(1, 1)
    chk.FontFace = iconFont()
    chk.Text = "check"
    chk.TextColor3 = Color3.fromRGB(255, 255, 255)
    chk.TextSize = 12
    chk.TextTransparency = default and 0 or 1
    chk.ZIndex = box.ZIndex + 1
    chk.Parent = box

    local lbl = Instance.new("TextLabel")
    lbl.Name = "label"
    lbl.BackgroundTransparency = 1
    lbl.AnchorPoint = Vector2.new(0, 0.5)
    lbl.Position = UDim2.new(0, 34, 0.5, 0)
    lbl.Size = cfg.KeybindFeature and UDim2.new(1, -90, 1, 0) or UDim2.new(1, -20, 1, 0)
    lbl.Font = Enum.Font.GothamMedium
    lbl.Text = key and t(key) or name
    lbl.TextColor3 = default and TEXT_ON or TEXT_OFF
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.ZIndex = row.ZIndex + 10
    lbl.Parent = row

    if key then registerTranslatable(lbl, key) end

    local sep = Instance.new("Frame")
    sep.Name = "sep"
    sep.BackgroundColor3 = Color3.fromRGB(50, 52, 60)
    sep.BackgroundTransparency = 0.4
    sep.BorderSizePixel = 0
    sep.AnchorPoint = Vector2.new(0, 1)
    sep.Position = UDim2.new(0, 8, 1, 0)
    sep.Size = UDim2.new(1, -16, 0, 1)
    sep.ZIndex = row.ZIndex + 2
    sep.Parent = row

    local state = { value = default, visible = true }

    local function paint()
        local ok, err = pcall(function()
            Tween:Create(box, TweenInfo.new(0.1), {
                BackgroundColor3 = state.value and accent or BOX_OFF,
            }):Play()
            Tween:Create(chk, TweenInfo.new(0.1), {
                TextTransparency = state.value and 0 or 1,
            }):Play()
            Tween:Create(lbl, TweenInfo.new(0.1), {
                TextColor3 = state.value and TEXT_ON or TEXT_OFF,
            }):Play()
        end)
    end

        local function toggle()
        state.value = not state.value
        paint()
        if setter then pcall(setter, state.value) end
        pcall(callback, state.value)
    end

    row.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            toggle()
        end
    end)

    row.MouseEnter:Connect(function()
        pcall(function()
            Tween:Create(rowBg, TweenInfo.new(0.12), { BackgroundTransparency = 0.82 }):Play()
            Tween:Create(lbl, TweenInfo.new(0.12), { TextColor3 = TEXT_HOVER }):Play()
        end)
    end)
    row.MouseLeave:Connect(function()
        pcall(function()
            Tween:Create(rowBg, TweenInfo.new(0.12), { BackgroundTransparency = 1 }):Play()
            Tween:Create(lbl, TweenInfo.new(0.12), {
                TextColor3 = state.value and TEXT_ON or TEXT_OFF,
            }):Play()
        end)
    end)

    paint()

        -- slot de keybind inline (opcional)
    local kbBtn = nil
    if cfg.KeybindFeature then
        kbBtn = Instance.new("TextButton")
        kbBtn.Name = "keybind"
        kbBtn.BackgroundColor3 = Color3.fromRGB(45, 48, 58)
        kbBtn.BorderSizePixel = 0
        kbBtn.AnchorPoint = Vector2.new(1, 0.5)
        kbBtn.Position = UDim2.new(1, -10, 0.5, 0)
        kbBtn.Size = UDim2.fromOffset(58, 18)
        kbBtn.Font = Enum.Font.GothamMedium
        kbBtn.Text = AR2_normalizeKey(AR2_CFG.keybinds[cfg.KeybindFeature] or "None")
        kbBtn.TextColor3 = Color3.fromRGB(220, 220, 230)
        kbBtn.TextSize = 10
        kbBtn.AutoButtonColor = false
        kbBtn.ZIndex = row.ZIndex + 15
        kbBtn.Parent = row

        local kc = Instance.new("UICorner")
        kc.CornerRadius = UDim.new(0, 3)
        kc.Parent = kbBtn

        local ks = Instance.new("UIStroke")
        ks.Color = accent
        ks.Thickness = 1
        ks.Transparency = 0.6
        ks.Parent = kbBtn

        local binding = false
        local armed_at = 0

        kbBtn.MouseButton1Click:Connect(function()
            if binding then
                binding = false
                kbBtn.Text = AR2_normalizeKey(AR2_CFG.keybinds[cfg.KeybindFeature] or "None")
                return
            end
            binding = true
            armed_at = os.clock()
            kbBtn.Text = "..."
        end)

        UserInput.InputBegan:Connect(function(input, gpe)
            if not binding then return end
            if os.clock() - armed_at < 0.15 then return end
            if gpe then return end
            local k
            if input.UserInputType == Enum.UserInputType.MouseButton1 then k = "MouseButton1"
            elseif input.UserInputType == Enum.UserInputType.MouseButton2 then k = "MouseButton2"
            elseif input.UserInputType == Enum.UserInputType.MouseButton3 then k = "MouseButton3"
            elseif input.UserInputType == Enum.UserInputType.Keyboard then k = input.KeyCode.Name
            end
            if not k then return end
            local norm = AR2_normalizeKey(k)
            AR2_CFG.keybinds[cfg.KeybindFeature] = norm
            kbBtn.Text = norm
            binding = false
        end)
    end

    local api = {}
    api.Root = row
    function api:GetValue() return state.value end
        function api:SetValue(v)
        state.value = v and true or false
        paint()
        if setter then pcall(setter, state.value) end
        pcall(callback, state.value)
    end
function api:SetValueSilent(v)
    state.value = v and true or false
    pcall(function()
        Tween:Create(box, TweenInfo.new(0.1), {
            BackgroundColor3 = state.value and accent or BOX_OFF,
        }):Play()
        Tween:Create(chk, TweenInfo.new(0.1), {
            TextTransparency = state.value and 0 or 1,
        }):Play()
        Tween:Create(lbl, TweenInfo.new(0.1), {
            TextColor3 = state.value and TEXT_ON or TEXT_OFF,
        }):Play()
    end)
end
    function api:SetVisible(v)
        state.visible = v and true or false
        row.Visible = state.visible
    end
    return api
end


local function makeThinSlider(sectionRoot, cfg)
    cfg = cfg or {}
    local name     = cfg.Name or "Value"
    local key      = cfg.TransKey
    local min      = cfg.Min or 0
    local max      = cfg.Max or 100
    local default  = cfg.Default or min
    local rounding = cfg.Rounding or 0
local callback = cfg.Callback or function() end
local setter   = cfg.Setter
local accent   = cfg.Accent or ACCENT_DEFAULT

    local row = Instance.new("Frame")
    row.Name = "slider"
    row.BackgroundColor3 = ROW_BG
    row.BackgroundTransparency = 1
    row.BorderSizePixel = 0
    row.Size = UDim2.new(1, -10, 0, 42)
    row.Parent = sectionRoot

    local lbl = Instance.new("TextLabel")
    lbl.BackgroundTransparency = 1
    lbl.Position = UDim2.new(0, 12, 0, 4)
    lbl.Size = UDim2.new(1, -80, 0, 14)
    lbl.Font = Enum.Font.GothamMedium
    lbl.Text = key and t(key) or name
    lbl.TextColor3 = TEXT_OFF
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.ZIndex = row.ZIndex + 10
    lbl.Parent = row

    if key then registerTranslatable(lbl, key) end

    local val = Instance.new("TextLabel")
    val.BackgroundTransparency = 1
    val.AnchorPoint = Vector2.new(1, 0)
    val.Position = UDim2.new(1, -12, 0, 4)
    val.Size = UDim2.new(0, 70, 0, 14)
    val.Font = Enum.Font.GothamBold
    val.Text = tostring(default)
    val.TextColor3 = accent
    val.TextSize = 13
    val.TextXAlignment = Enum.TextXAlignment.Right
    val.ZIndex = row.ZIndex + 10
    val.Parent = row

    local bar = Instance.new("Frame")
    bar.Name = "bar"
    bar.BackgroundColor3 = Color3.fromRGB(35, 38, 44)
    bar.BorderSizePixel = 0
    bar.Position = UDim2.new(0, 12, 0, 26)
    bar.Size = UDim2.new(1, -24, 0, 6)
    bar.ZIndex = row.ZIndex + 10
    bar.Parent = row

    local barCorner = Instance.new("UICorner")
    barCorner.CornerRadius = UDim.new(1, 0)
    barCorner.Parent = bar

    local barStroke = Instance.new("UIStroke")
    barStroke.Color = accent
    barStroke.Thickness = 1
    barStroke.Transparency = 0.65
    barStroke.Parent = bar

    local fill = Instance.new("Frame")
    fill.Name = "fill"
    fill.BackgroundColor3 = accent
    fill.BorderSizePixel = 0
    fill.Size = UDim2.fromScale((default - min) / math.max(max - min, 1), 1)
    fill.ZIndex = row.ZIndex + 11
    fill.Parent = bar

    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(1, 0)
    fillCorner.Parent = fill

    local sep = Instance.new("Frame")
    sep.BackgroundColor3 = Color3.fromRGB(50, 52, 60)
    sep.BackgroundTransparency = 0.4
    sep.BorderSizePixel = 0
    sep.AnchorPoint = Vector2.new(0, 1)
    sep.Position = UDim2.new(0, 8, 1, 0)
    sep.Size = UDim2.new(1, -16, 0, 1)
    sep.ZIndex = row.ZIndex + 2
    sep.Parent = row

    local state = { value = default, visible = true }

    local function round(v)
        if rounding <= 0 then return math.floor(v + 0.5) end
        local mult = 10 ^ rounding
        return math.floor(v * mult + 0.5) / mult
    end

    local function paint()
        pcall(function()
            val.Text = tostring(state.value)
            local pct = (state.value - min) / math.max(max - min, 1)
            fill.Size = UDim2.fromScale(math.clamp(pct, 0, 1), 1)
        end)
    end

local function setFromX(px)
    local abs, sz = bar.AbsolutePosition, bar.AbsoluteSize
    local pct = math.clamp((px - abs.X) / math.max(sz.X, 1), 0, 1)
    local raw = min + (max - min) * pct
    state.value = round(raw)
    paint()
    if setter then pcall(setter, state.value) end
    pcall(callback, state.value)
end

    local holding = false
    row.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            holding = true
            setFromX(UserInput:GetMouseLocation().X)
        end
    end)
    UserInput.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then holding = false end
    end)
    UserInput.InputChanged:Connect(function(i)
        if holding and i.UserInputType == Enum.UserInputType.MouseMovement then
            setFromX(i.Position.X)
        end
    end)

    paint()

    local api = {}
    api.Root = row
    function api:GetValue() return state.value end
    function api:SetValue(v)
        if type(v) ~= "number" then return end
        state.value = v
        paint()
        if setter then pcall(setter, state.value) end
        pcall(callback, state.value)
    end
        function api:SetValueSilent(v)
        if type(v) ~= "number" then return end
        state.value = v
        paint()
    end
    function api:SetVisible(v)
        state.visible = v and true or false
        row.Visible = state.visible
    end
    return api
end

local function makeKeybindRow(sectionRoot, cfg)
    cfg = cfg or {}
    local name     = cfg.Name or "Keybind"
    local key      = cfg.TransKey
    local default  = cfg.Default or "None"
     local callback = cfg.Callback or function() end
    local setter   = cfg.Setter
    local accent   = cfg.Accent or ACCENT_DEFAULT

    local row = Instance.new("Frame")
    row.Name = "keybindRow"
    row.BackgroundColor3 = ROW_BG
    row.BackgroundTransparency = 1
    row.BorderSizePixel = 0
    row.Size = UDim2.new(1, -10, 0, 28)
    row.Parent = sectionRoot

    local rowBg = Instance.new("Frame")
    rowBg.Name = "rowBg"
    rowBg.BackgroundColor3 = ROW_BG_HOVER
    rowBg.BackgroundTransparency = 1
    rowBg.BorderSizePixel = 0
    rowBg.Size = UDim2.fromScale(1, 1)
    rowBg.ZIndex = row.ZIndex + 1
    rowBg.Parent = row

    local lbl = Instance.new("TextLabel")
    lbl.BackgroundTransparency = 1
    lbl.AnchorPoint = Vector2.new(0, 0.5)
    lbl.Position = UDim2.new(0, 12, 0.5, 0)
    lbl.Size = UDim2.new(1, -20, 1, 0)
    if cfg.KeybindFeature then
    lbl.Size = UDim2.new(1, -90, 1, 0)
end
    lbl.Font = Enum.Font.GothamMedium
    lbl.Text = key and t(key) or name
    lbl.TextColor3 = TEXT_OFF
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.ZIndex = row.ZIndex + 10
    lbl.Parent = row

    if key then registerTranslatable(lbl, key) end

    local bindBtn = Instance.new("TextButton")
    bindBtn.Name = "bind"
    bindBtn.BackgroundColor3 = Color3.fromRGB(45, 48, 58)
    bindBtn.BorderSizePixel = 0
    bindBtn.AnchorPoint = Vector2.new(1, 0.5)
    bindBtn.Position = UDim2.new(1, -10, 0.5, 0)
    bindBtn.Size = UDim2.fromOffset(60, 20)
    bindBtn.Font = Enum.Font.GothamMedium
    bindBtn.Text = AR2_normalizeKey(default)
    bindBtn.TextColor3 = Color3.fromRGB(220, 220, 230)
    bindBtn.TextSize = 11
    bindBtn.AutoButtonColor = false
    bindBtn.ZIndex = row.ZIndex + 12
    bindBtn.Parent = row

    local kc = Instance.new("UICorner")
    kc.CornerRadius = UDim.new(0, 3)
    kc.Parent = bindBtn

    local ks = Instance.new("UIStroke")
    ks.Color = accent
    ks.Thickness = 1.5
    ks.Transparency = 0.5
    ks.Parent = bindBtn

    local sep = Instance.new("Frame")
    sep.BackgroundColor3 = Color3.fromRGB(50, 52, 60)
    sep.BackgroundTransparency = 0.4
    sep.BorderSizePixel = 0
    sep.AnchorPoint = Vector2.new(0, 1)
    sep.Position = UDim2.new(0, 8, 1, 0)
    sep.Size = UDim2.new(1, -16, 0, 1)
    sep.ZIndex = row.ZIndex + 2
    sep.Parent = row

    local state = { value = AR2_normalizeKey(default), visible = true, binding = false, armed_at = 0 }

    bindBtn.MouseButton1Click:Connect(function()
        if state.binding then
            state.binding = false
            bindBtn.Text = state.value
            return
        end
        state.binding = true
        state.armed_at = os.clock()
        bindBtn.Text = "..."
    end)

   UserInput.InputBegan:Connect(function(input, gpe)
        if not state.binding then return end
        if os.clock() - state.armed_at < 0.15 then return end
        if gpe then return end
        local k
        if input.UserInputType == Enum.UserInputType.MouseButton1 then k = "MouseButton1"
        elseif input.UserInputType == Enum.UserInputType.MouseButton2 then k = "MouseButton2"
        elseif input.UserInputType == Enum.UserInputType.MouseButton3 then k = "MouseButton3"
        elseif input.UserInputType == Enum.UserInputType.Keyboard then k = input.KeyCode.Name
        end
        if not k then return end
        state.value = AR2_normalizeKey(k)
        state.binding = false
        bindBtn.Text = state.value
        pcall(callback, state.value)
        if setter then pcall(setter, state.value) end
    end)

    row.MouseEnter:Connect(function()
        pcall(function()
            Tween:Create(rowBg, TweenInfo.new(0.12), { BackgroundTransparency = 0.82 }):Play()
            Tween:Create(lbl, TweenInfo.new(0.12), { TextColor3 = TEXT_HOVER }):Play()
        end)
    end)
    row.MouseLeave:Connect(function()
        pcall(function()
            Tween:Create(rowBg, TweenInfo.new(0.12), { BackgroundTransparency = 1 }):Play()
            Tween:Create(lbl, TweenInfo.new(0.12), { TextColor3 = TEXT_OFF }):Play()
        end)
    end)

    local api = {}
    api.Root = row
    function api:GetValue() return state.value end
    function api:SetValue(v)
        state.value = AR2_normalizeKey(v)
        bindBtn.Text = state.value
        pcall(callback, state.value)
        if setter then pcall(setter, state.value) end
    end
    function api:SetValueSilent(v)
        state.value = AR2_normalizeKey(v)
        bindBtn.Text = state.value
    end
    function api:SetVisible(v)
        state.visible = v and true or false
        row.Visible = state.visible
    end
    return api
end

-- ============================================================
-- 56. PATCH NAS SECTIONS DA NL
-- ============================================================
 local function patchSection(section)
    if not section or section.__tetescr_patched then return section end
    section.__tetescr_patched = true

    local anchorLbl = section:AddLabel(" ")
    if not anchorLbl or not anchorLbl.Root then
        warn("[Tetescr hub] patchSection: AddLabel nao retornou Root")
        section.Root = nil
        return section
    end

    local basedFrame = anchorLbl.Root

    -- esconde os filhos originais do label (texto, linha separadora, handler vazio)
    pcall(function()
        for _, c in ipairs(basedFrame:GetChildren()) do
            c.Visible = false
        end
    end)

    -- ajusta o BasedFrame pra ficar com altura automatica
    basedFrame.Size = UDim2.new(1, 0, 0, 0)
    basedFrame.BackgroundTransparency = 1
    basedFrame.AutomaticSize = Enum.AutomaticSize.Y
    basedFrame.ClipsDescendants = false

    -- adiciona um UIListLayout proprio
    local layout = Instance.new("UIListLayout")
    layout.FillDirection = Enum.FillDirection.Vertical
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Padding = UDim.new(0, 3)
    layout.Parent = basedFrame

    section.Root = basedFrame

    function section:AddCheckRow(cfg)
        return makeCheckRow(self.Root, cfg)
    end
    function section:AddThinSlider(cfg)
        return makeThinSlider(self.Root, cfg)
    end
    function section:AddKeybindRow(cfg)
        return makeKeybindRow(self.Root, cfg)
    end
    return section
end

  
-- ============================================================
-- 57. TRACK ROWS / REFRESH / SYNC
-- ============================================================
AR2_UI_ELEMENTS = AR2_UI_ELEMENTS or {}
getgenv().AR2_UI_ELEMENTS = AR2_UI_ELEMENTS
local function trackRow(elem, getter, setter)
    if elem then
        table.insert(AR2_UI_ELEMENTS, {
            getter = getter,
            setter = setter,
            elem = elem
        })
    end
end

function AR2_refreshUI()
    for _, e in ipairs(AR2_UI_ELEMENTS) do
        if e.getter and e.elem and e.elem.SetValueSilent then
            local ok, v = pcall(e.getter)
            if ok then pcall(e.elem.SetValueSilent, e.elem, v) end
        end
    end
end
getgenv().AR2_refreshUI = AR2_refreshUI

function AR2_syncAllSliders()
    AR2_refreshUI()
end
getgenv().AR2_syncAllSliders = AR2_syncAllSliders

local function registerSectionLabel(section, key)
    if not section or not section.Root then return end
    pcall(function()
        for _, child in ipairs(section.Root:GetChildren()) do
            if child:IsA("TextLabel") then
                registerTranslatable(child, key)
                child.Text = t(key)
                break
            end
        end
    end)
end

-- ============================================================
-- 58. CRIA A JANELA
-- ============================================================
local win = NL:CreateWindow({
    Name = "Tetescr hub",
    Content = "ar2",
    Size = UDim2.fromOffset(1000, 640),
    ConfigFolder = "tetescrHUB_Configs",
    Enable3DRenderer = false,
    Keybind = "P",
    Theme = "Dark",
    Accent = ACCENT_DEFAULT,
    Logo = "",
})
if getgenv then getgenv().TetescrHUB_Window = win end

-- ============================================================
-- 59. DESTRÓI LOGO E PERFIL DA NL
-- ============================================================
task.spawn(function()
    task.wait(0.2)
    local sg = NL.ScreenGui
    if not sg then return end
    for _, d in ipairs(sg:GetDescendants()) do
        if d:IsA("ImageLabel") and d.Size == UDim2.new(0, 35, 0, 35) then
            pcall(function() d:Destroy() end)
        end
    end
end)

-- ============================================================
-- 60. ORDEM DAS TABS
-- ============================================================
local TAB_ORDER = {
    { name = "ESP",       key = "tab_esp"      },
    { name = "Combat",    key = "tab_combat"   },
    { name = "Movement",  key = "tab_movement" },
    { name = "Cars",      key = "tab_cars"     },
    { name = "World",     key = "tab_world"    },
    { name = "Radar",     key = "tab_radar"    },
    { name = "Teleport",  key = "tab_teleport" },
    { name = "Diversos",  key = "tab_diversos" },
    { name = "Cosmetics", key = "tab_cos"      },
    { name = "Config",    key = "tab_config"   },
}

local TABS = {}
for _, entry in ipairs(TAB_ORDER) do
    TABS[entry.name] = win:AddTab({ Icon = "", Name = t(entry.key) })
end

local CAT = {
    ["ESP"]       = Color3.fromRGB(90, 220, 120),
    ["Combat"]    = Color3.fromRGB(255, 90, 90),
    ["Movement"]  = Color3.fromRGB(90, 180, 255),
    ["Cars"]      = Color3.fromRGB(255, 200, 60),
    ["World"]     = Color3.fromRGB(120, 200, 255),
    ["Radar"]     = Color3.fromRGB(255, 150, 60),
    ["Teleport"]  = Color3.fromRGB(180, 130, 255),
    ["Diversos"]  = Color3.fromRGB(200, 200, 210),
    ["Cosmetics"] = Color3.fromRGB(255, 130, 220),
    ["Config"]    = Color3.fromRGB(180, 180, 190),
}

-- ============================================================
-- 60b. ESCONDE SIDEBAR DA NL + CHIPS HORIZONTAIS NO TOPO
-- ============================================================
task.spawn(function()
    task.wait(0.35)
    local sg = NL.ScreenGui
    if not sg then return end

    local windowFrame, leftMenu, rightMenu
    for _, obj in ipairs(sg:GetDescendants()) do
        if obj:IsA("Frame") then
            local sx = obj.Size.X.Offset
            local sy = obj.Size.Y.Offset
            if (sx >= 980 and sx <= 1020 and sy >= 620 and sy <= 660)
            or (sx >= 760 and sx <= 840 and sy >= 740 and sy <= 820) then
                windowFrame = obj
            end
        end
    end

    if not windowFrame then
        warn("[Tetescr hub] windowFrame não encontrado — sidebar da NL fica visível")
        return
    end

    for _, child in ipairs(windowFrame:GetChildren()) do
        if child:IsA("Frame") then
            if child.Size.X.Offset == 165 then
                leftMenu = child
            elseif child.Position.X.Offset == 166 and child.Size.X.Offset < 0 then
                rightMenu = child
            end
        end
    end

    if leftMenu then
        leftMenu.Visible = false
        leftMenu.ClipsDescendants = true
    end

    if rightMenu then
        rightMenu.Position = UDim2.new(0, 0, 0, 0)
        rightMenu.Size = UDim2.new(1, 0, 1, 0)
        rightMenu.ClipsDescendants = true
    end

    for _, child in ipairs(windowFrame:GetChildren()) do
        if child:IsA("Frame") and child ~= rightMenu and child ~= leftMenu then
            if child.Name:lower():find("head") or child.Size.Y.Offset == 50 then
                child.Visible = false
            end
        end
    end
    local header = Instance.new("Frame")
    header.Name = "tetescr_header"
    header.BackgroundColor3 = Color3.fromRGB(14, 14, 16)
    header.BackgroundTransparency = 0
    header.BorderSizePixel = 0
    header.Position = UDim2.new(0, 0, 0, 0)
    header.Size = UDim2.new(1, 0, 0, 32)
    header.ZIndex = 200
    header.Parent = rightMenu

    local title = Instance.new("TextLabel")
    title.Name = "title"
    title.BackgroundTransparency = 1
    title.Position = UDim2.fromOffset(12, 0)
    title.Size = UDim2.new(0, 80, 1, 0)
    title.Font = Enum.Font.GothamBold
    title.Text = "Tetescr hub"
    title.TextSize = 15
    title.TextColor3 = Color3.fromRGB(235, 235, 245)
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.TextYAlignment = Enum.TextYAlignment.Center
    title.ZIndex = 201
    title.Parent = header

    local subTitle = Instance.new("TextLabel")
    subTitle.Name = "subtitle"
    subTitle.BackgroundTransparency = 1
    subTitle.Position = UDim2.fromOffset(95, 0)
    subTitle.Size = UDim2.new(0, 60, 1, 0)
    subTitle.Font = Enum.Font.Gotham
    subTitle.Text = "ar2"
    subTitle.TextSize = 10
    subTitle.TextColor3 = Color3.fromRGB(150, 152, 160)
    subTitle.TextXAlignment = Enum.TextXAlignment.Left
    subTitle.TextYAlignment = Enum.TextYAlignment.Center
    subTitle.ZIndex = 201
    subTitle.Parent = header

    local sepLine = Instance.new("Frame")
    sepLine.BackgroundColor3 = Color3.fromRGB(40, 42, 50)
    sepLine.BorderSizePixel = 0
    sepLine.Position = UDim2.new(0, 0, 1, 0)
    sepLine.Size = UDim2.new(1, 0, 0, 1)
    sepLine.ZIndex = 200
    sepLine.Parent = header

    local tabBar = Instance.new("Frame")
    tabBar.Name = "tetescr_tabbar"
    tabBar.BackgroundColor3 = Color3.fromRGB(14, 14, 16)
    tabBar.BackgroundTransparency = 0
    tabBar.BorderSizePixel = 0
    tabBar.Position = UDim2.new(0, 0, 0, 32)
    tabBar.Size = UDim2.new(1, 0, 0, 42)
    tabBar.ZIndex = 198
    tabBar.Parent = rightMenu

    local sepLine = Instance.new("Frame")
    sepLine.BackgroundColor3 = Color3.fromRGB(40, 42, 50)
    sepLine.BorderSizePixel = 0
    sepLine.Position = UDim2.new(0, 0, 1, 0)
    sepLine.Size = UDim2.new(1, 0, 0, 1)
    sepLine.ZIndex = 200
    sepLine.Parent = header

    local chipLayout = Instance.new("UIListLayout")
    chipLayout.FillDirection = Enum.FillDirection.Horizontal
    chipLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
    chipLayout.VerticalAlignment = Enum.VerticalAlignment.Center
    chipLayout.Padding = UDim.new(0, 6)
    chipLayout.SortOrder = Enum.SortOrder.LayoutOrder
    chipLayout.Parent = tabBar

    local chipPad = Instance.new("UIPadding")
    chipPad.PaddingLeft = UDim.new(0, 10)
    chipPad.PaddingRight = UDim.new(0, 10)
    chipPad.Parent = tabBar

    local chipButtons = {}
    local chipRefs = {}

    local function setActiveChip(idx)
        for i, btn in ipairs(chipButtons) do
            local isActive = (i == idx)
            local color = btn:GetAttribute("accent") or ACCENT_DEFAULT
            btn:SetAttribute("active", isActive)
            Tween:Create(btn, TweenInfo.new(0.15), {
                BackgroundColor3 = isActive and Color3.fromRGB(50, 55, 65) or Color3.fromRGB(28, 30, 36),
                TextColor3 = isActive and color or Color3.fromRGB(200, 200, 210),
            }):Play()
            local stroke = btn:FindFirstChildOfClass("UIStroke")
            if stroke then
                stroke.Color = color
                stroke.Transparency = isActive and 0.2 or 0.75
            end
        end
    end

    for i, entry in ipairs(TAB_ORDER) do
        local tab = TABS[entry.name]
        local color = CAT[entry.name] or ACCENT_DEFAULT

        local btn = Instance.new("TextButton")
        btn.Name = "chip_" .. entry.name
        btn.BackgroundColor3 = Color3.fromRGB(28, 30, 36)
        btn.BorderSizePixel = 0
        btn.AutoButtonColor = false
        btn.Font = Enum.Font.GothamBold
        btn.Text = t(entry.key)
        btn.TextColor3 = Color3.fromRGB(200, 200, 210)
        btn.TextSize = 12
        btn.ZIndex = 202

        local ts = TextSvc:GetTextSize(t(entry.key), 12, Enum.Font.GothamBold, Vector2.new(1000, 1000))
        btn.Size = UDim2.fromOffset(ts.X + 24, 26)

        local c = Instance.new("UICorner")
        c.CornerRadius = UDim.new(0, 3)
        c.Parent = btn

        local s = Instance.new("UIStroke")
        s.Color = color
        s.Thickness = 1
        s.Transparency = 0.75
        s.Parent = btn

        btn:SetAttribute("accent", color)
        btn.LayoutOrder = i
        btn.Parent = tabBar

        btn.MouseEnter:Connect(function()
            if btn:GetAttribute("active") then return end
            Tween:Create(btn, TweenInfo.new(0.1), {
                BackgroundColor3 = Color3.fromRGB(40, 44, 52),
            }):Play()
        end)
        btn.MouseLeave:Connect(function()
            if btn:GetAttribute("active") then return end
            Tween:Create(btn, TweenInfo.new(0.1), {
                BackgroundColor3 = Color3.fromRGB(28, 30, 36),
            }):Play()
        end)

        btn.MouseButton1Click:Connect(function()
            for _, t2 in ipairs(win.Tabs) do
                pcall(function() t2.SetValue(false) end)
            end
            pcall(function() tab.SetValue(true) end)
            setActiveChip(i)
            pcall(function() NL:SetAccentColor(color) end)
        end)

        chipButtons[i] = btn
        table.insert(chipRefs, { btn = btn, key = entry.key })
        registerTranslatable(btn, entry.key)
    end

    for _, child in ipairs(rightMenu:GetChildren()) do
        if child:IsA("Frame") and child.Position.Y.Offset == 50 and child.Size.Y.Offset < 0 then
                        child.Position = UDim2.new(0, 0, 0, 74)
                       child.Size = UDim2.new(1, 0, 1, -74)
        end
    end

    setActiveChip(1)
        if win.Tabs and win.Tabs[1] then
        for _, t2 in ipairs(win.Tabs) do
            pcall(function() t2.SetValue(false) end)
        end
        pcall(function() win.Tabs[1].SetValue(true) end)
        pcall(function() win.CurrentTab = 1 end)
    end

    local origApply = applyLocale
    applyLocale = function(lang)
        origApply(lang)
        task.wait(0.05)
        for _, ref in ipairs(chipRefs) do
            pcall(function()
                local newText = t(ref.key)
                local size = TextSvc:GetTextSize(newText, 12, Enum.Font.GothamBold, Vector2.new(1000, 1000))
                ref.btn.Size = UDim2.fromOffset(size.X + 24, 26)
            end)
        end
    end
    getgenv().TetescrHUB_applyLocale = applyLocale

    print("[Tetescr hub] chips horizontais montados")
end)


-- ============================================================
-- 61. HELPERS R / S / K
-- ============================================================
local function R(section, name, key, cfgTable, cfgKey, cat, callback, keybindFeature)
    local accent = CAT[cat]
    local elem = section:AddCheckRow({
        Name = name,
        TransKey = key,
        Default = cfgTable[cfgKey] and true or false,
        Accent = accent,
        KeybindFeature = keybindFeature,
        Setter = function(v) cfgTable[cfgKey] = v end,
        Callback = callback,
    })
    trackRow(elem,
        function() return cfgTable[cfgKey] and true or false end,
        function(v) if elem and elem.SetValue then elem.SetValue(v) end end)
    return elem
end

local function S(section, name, key, cfgTable, cfgKey, min, max, cat, rounding, callback)
    local accent = CAT[cat]
    local elem = section:AddThinSlider({
        Name = name,
        TransKey = key,
        Min = min, Max = max,
        Default = cfgTable[cfgKey] or min,
        Rounding = rounding or 0,
        Accent = accent,
        Setter = function(v) cfgTable[cfgKey] = v end,
        Callback = callback,
    })
    trackRow(elem,
        function() return cfgTable[cfgKey] or min end,
        function(v) if elem and elem.SetValue then elem.SetValue(v) end end)
    return elem
end

local function K(section, name, key, cfgTable, cfgKey, cat, callback)
    local accent = CAT[cat]
    local elem = section:AddKeybindRow({
        Name = name,
        TransKey = key,
        Default = cfgTable[cfgKey] or "None",
        Accent = accent,
        Setter = function(v)
            cfgTable[cfgKey] = v
            if callback then pcall(callback, v) end
        end,
 
        Callback = callback,
    })
    trackRow(elem,
        function() return cfgTable[cfgKey] or "None" end,
        function(v) if elem and elem.SetValue then elem.SetValue(v) end end)
    return elem
end
local function D(section, name, key, cfgTable, cfgKey, values, cat, callback)
    local accent = CAT[cat]
    local elem = section:AddLabel(key and t(key) or name):AddDropdown({
        Values   = values,
        Default  = cfgTable[cfgKey],
        Callback = function(v)
            cfgTable[cfgKey] = v
            if callback then pcall(callback, v) end
        end,
    })
    trackRow(elem,
        function() return cfgTable[cfgKey] end,
        function(v) if elem and elem.SetValue then elem.SetValue(v) end end)
    return elem
end
local function C(section, name, key, cfgTable, cfgKey, cat, callback)
    local elem = section:AddLabel(key and t(key) or name):AddColorPicker({
        Default = cfgTable[cfgKey],
        Callback = function(v)
            cfgTable[cfgKey] = v
            if callback then pcall(callback, v) end
        end,
    })
    trackRow(elem,
        function() return cfgTable[cfgKey] end,
        function(v) if elem and elem.SetValue then elem.SetValue(v) end end)
    return elem
end

-- ============================================================
-- 62. ESP
-- ============================================================
do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = t("sec_player_visuals") }))
    registerSectionLabel(sec, "sec_player_visuals")
    R(sec, "Enable ESP", "opt_enable_esp", AR2_CFG.players, "on", "ESP",
    function() AR2_esp.update() end, "playerESP")
    R(sec, "Player Name", "opt_player_name",     AR2_CFG.players, "name",        "ESP")
    R(sec, "Player Box",  "opt_player_box",      AR2_CFG.players, "box",         "ESP")
    R(sec, "Health Bar",  "opt_player_health",   AR2_CFG.players, "healthBar",   "ESP")
    R(sec, "Distance",    "opt_player_distance", AR2_CFG.players, "dist",        "ESP")
    R(sec, "Skeleton",    "opt_player_skeleton", AR2_CFG.players, "skeleton",    "ESP")
    R(sec, "Chams",       "opt_player_chams",    AR2_CFG.players, "chams",       "ESP")
    R(sec, "Tracer",      "opt_player_tracer",   AR2_CFG.players, "tracer",      "ESP")
    R(sec, "Weapon",      "opt_player_weapon",   AR2_CFG.players, "weapon",      "ESP")

end

do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = t("sec_filters"), Position = "right" }))
    registerSectionLabel(sec, "sec_filters")
    R(sec, "Ignore Teammates", "opt_ignore_teammates", AR2_CFG.magicAim, "noTeammate", "ESP")
    R(sec, "Visible Only",     "opt_visible_only",     AR2_CFG.magicAim, "visCheck",   "ESP")
    S(sec, "Render Distance",  "opt_render_distance",  AR2_CFG.players,  "maxDist", 100, 5000, "ESP")
    --(sec, "Zombie ESP",       "opt_zombie_esp",       AR2_CFG.zombies,  "on", "ESP", function() AR2_esp.update() end)
    --S(sec, "Zombie Distance",  "opt_zombie_distance",  AR2_CFG.zombies,  "maxDist", 100, 3000, "ESP")
    R(sec, "Loot ESP",         "opt_loot_esp",         AR2_CFG.loot,     "on", "ESP", function() AR2_esp.update() end, "lootESP")
    S(sec, "Loot Distance",    "opt_loot_distance",    AR2_CFG.loot,     "maxDist", 100, 5000, "ESP")
R(sec, "Corpse ESP",       "opt_corpse_esp",       AR2_CFG.corpses,  "on", "ESP", function() AR2_esp.update() end, "corpseESP")
R(sec, "Vehicle ESP",      "opt_vehicle_esp",      AR2_CFG.vehicles, "on", "ESP", function() AR2_esp.update() end, "vehicleESP")

end

do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = "Loot ESP" }))
    registerSectionLabel(sec, "opt_loot_esp")
    R(sec, "Loot Box",   "opt_loot_box",   AR2_CFG.loot, "box",   "ESP", function() AR2_esp.update() end)
    R(sec, "Loot Name",  "opt_loot_name",  AR2_CFG.loot, "name",  "ESP", function() AR2_esp.update() end)
    R(sec, "Loot Chams", "opt_loot_chams", AR2_CFG.loot, "chams", "ESP", function() AR2_esp.update() end)
end

do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = "Corpse ESP", Position = "right" }))
    registerSectionLabel(sec, "opt_corpse_esp")
    R(sec, "Corpse Box",      "opt_corpse_box",  AR2_CFG.corpses, "box",         "ESP", function() AR2_esp.update() end)
    R(sec, "Corpse Name",     "opt_corpse_name", AR2_CFG.corpses, "name",        "ESP", function() AR2_esp.update() end)
    R(sec, "Corpse Distance", "opt_corpse_dist", AR2_CFG.corpses, "dist",        "ESP", function() AR2_esp.update() end)
    R(sec, "Players Only",    "opt_corpse_only", AR2_CFG.corpses, "playersOnly", "ESP", function() AR2_esp.update() end)
end

do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = "Vehicle ESP" }))
    registerSectionLabel(sec, "opt_vehicle_esp")
    R(sec, "Vehicle Box",   "opt_veh_box",   AR2_CFG.vehicles, "box",   "ESP", function() AR2_esp.update() end)
    R(sec, "Vehicle Name",  "opt_veh_name",  AR2_CFG.vehicles, "name",  "ESP", function() AR2_esp.update() end)
    R(sec, "Vehicle Chams", "opt_veh_chams", AR2_CFG.vehicles, "chams", "ESP", function() AR2_esp.update() end)
end

do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = "ESP Style" }))
    registerSectionLabel(sec, "opt_esp_style")
    R(sec, "Corner Box",         "opt_esp_corner_box",    AR2_CFG.style, "cornerBox",         "ESP")
    R(sec, "Box Fill",           "opt_esp_fill",          AR2_CFG.style, "fill",              "ESP")
    R(sec, "Health Numbers",     "opt_esp_health_text",   AR2_CFG.style, "healthText",        "ESP")
    R(sec, "Tracer From Bottom", "opt_esp_tracer_bottom", AR2_CFG.style, "tracerFromBottom",  "ESP")
    S(sec, "Box Thickness",      "opt_esp_box_thickness", AR2_CFG.style, "boxThickness",      1, 5, "ESP")
    S(sec, "Tracer Thickness",   "opt_esp_tracer_thick",  AR2_CFG.style, "tracerThickness",   1, 5, "ESP")
    S(sec, "Text Size",          "opt_esp_text_size",     AR2_CFG.style, "textSize",          9, 22, "ESP")
end

do
    local sec = patchSection(TABS["ESP"]:AddSection({ Name = "ESP Colors", Position = "right" }))
    registerSectionLabel(sec, "opt_esp_colors")
    C(sec, "Player Color",   "opt_esp_color_player",  AR2_CFG.colors, "player",  "ESP",
        function(v) AR2_COLORS.player = v end)
    C(sec, "Armed Player",   "opt_esp_color_armed",   AR2_CFG.colors, "armed",   "ESP",
        function(v) AR2_COLORS.armed = v end)
    C(sec, "Zombie Color",   "opt_esp_color_zombie",  AR2_CFG.colors, "zombie",  "ESP",
        function(v) AR2_COLORS.zombie = v end)
    C(sec, "Zombie (Agro)",  "opt_esp_color_agro",    AR2_CFG.colors, "agro",    "ESP",
        function(v) AR2_COLORS.agro = v end)
    C(sec, "Vehicle Color",  "opt_esp_color_vehicle", AR2_CFG.colors, "vehicle", "ESP",
        function(v) AR2_COLORS.vehicle = v end)
    C(sec, "Info Text",      "opt_esp_color_info",    AR2_CFG.colors, "info",    "ESP",
        function(v) AR2_COLORS.info = v end)
end

-- ============================================================
-- 63. COMBAT
-- ============================================================

do
    local sec = patchSection(TABS["Combat"]:AddSection({ Name = t("sec_aim") }))
    registerSectionLabel(sec, "sec_aim")

    R(sec, "Silent Aim",       "opt_silent_aim",       AR2_CFG.magicAim, "on", "Combat", function() AR2_magicUpdate() end, "silentAim")
    S(sec, "Silent FOV",    "opt_silent_fov",     AR2_CFG.magicAim, "fov",            20, 800,  "Combat", nil, function() AR2_magicUpdate() end)
    S(sec, "Max Distance",  "opt_max_dist",       AR2_CFG.magicAim, "maxDist",        100, 10000, "Combat")
    D(sec, "Target Part",   "opt_target_part",    AR2_CFG.magicAim, "part",
        { "Head", "HeadCollider", "UpperTorso", "Root" }, "Combat")

    S(sec, "Max Bend",      "opt_max_bend",       AR2_CFG.magicAim, "maxBend",        1, 180, "Combat")
    S(sec, "Redirect %",    "opt_redirect",       AR2_CFG.magicAim, "redirectChance", 0, 100, "Combat")

    R(sec, "Target Players","opt_target_players", AR2_CFG.magicAim, "players",        "Combat", function() AR2_magicUpdate() end)
    R(sec, "Target Zombies","opt_target_zombies", AR2_CFG.magicAim, "zombies",        "Combat", function() AR2_magicUpdate() end)
    R(sec, "Skip Teammates","opt_no_teammate",    AR2_CFG.magicAim, "noTeammate",     "Combat")
    R(sec, "Visible Only",  "opt_vis_check",      AR2_CFG.magicAim, "visCheck",       "Combat")

    R(sec, "Show FOV",      "opt_show_fov",       AR2_CFG.magicAim, "showFov",        "Combat", function() AR2_magicUpdate() end)
    R(sec, "Show Lock Line","opt_show_lock",      AR2_CFG.magicAim, "showLockLine",   "Combat", function() AR2_magicUpdate() end)
    R(sec, "Ignore FOV",    "opt_ignore_fov",     AR2_CFG.magicAim, "ignoreFov",      "Combat", function() AR2_magicUpdate() end)

    R(sec, "Kill Spread",      "opt_kill_spread",  AR2_CFG.magicAim, "killSpread",   "Combat")
    R(sec, "Origin Shift",     "opt_origin_shift", AR2_CFG.magicAim, "originShift",  "Combat")
    R(sec, "Drop Compensation", "opt_drop_comp",   AR2_CFG.magicAim, "dropComp",     "Combat")

    R(sec, "Wall Check",    "opt_wall_check",     AR2_CFG.aim,       "wallCheck",    "Combat")
R(sec, "Rage Bot", "opt_rage_bot", AR2_CFG.rageBot, "on", "Combat",
    function(v) AR2_rageBotSetEnabled(v) end, "rageBot")
S(sec, "Max Distance",   "opt_rb_max_dist",     AR2_CFG.rageBot, "maxDist",     50, 2000,  "Combat")
S(sec, "FOV",            "opt_rb_fov",          AR2_CFG.rageBot, "fov",         50, 500,   "Combat")
R(sec, "Ignore FOV",     "opt_rb_ignore_fov",   AR2_CFG.rageBot, "ignoreFov",   "Combat")
R(sec, "Visible Only",   "opt_rb_vis_check",    AR2_CFG.rageBot, "visCheck",    "Combat")
R(sec, "Shoot from Inventory", "opt_rb_inventory_shoot", AR2_CFG.rageBot, "inventoryShoot", "Combat")
R(sec, "Require ADS",    "opt_rb_require_ads",  AR2_CFG.rageBot, "requireADS",  "Combat")
R(sec, "Force ADS",      "opt_rb_force_ads",    AR2_CFG.rageBot, "forceADS",    "Combat")
S(sec, "ADS Min Distance","opt_rb_ads_min_dist",AR2_CFG.rageBot, "adsMinDist",  0, 1000,   "Combat")
end

do
    local sec = patchSection(TABS["Combat"]:AddSection({ Name = t("sec_weapon"), Position = "right" }))
    registerSectionLabel(sec, "sec_weapon")
    R(sec, "No Recoil",   "opt_no_recoil",   AR2_CFG.weapon, "noRecoil",   "Combat")
    S(sec, "Recoil Reduction %", "opt_recoil_reduction", AR2_CFG.weapon, "recoilReduction", 0, 100, "Combat")
    R(sec, "No Spread",   "opt_no_spread",   AR2_CFG.weapon, "noSpread",   "Combat")
    R(sec, "Wallbang",    "opt_wallbang",    AR2_CFG.weapon, "wallbang",   "Combat",
        function(v) if v then startWallbangTagging() else stopWallbangTagging() end end)
    R(sec, "Instant Reload", "opt_instant_reload", AR2_CFG.weapon, "instantReload", "Combat",
        function(v) setInstantReloadEnabled(v) end)
    R(sec, "Auto Reload",   "opt_auto_reload",    AR2_CFG.weapon, "autoReload",   "Combat",
        function(v) AR2_setAutoReload(v) end)
    R(sec, "All Fire Modes", "opt_all_fire_modes", AR2_CFG.weapon, "allFireModes", "Combat",
        function(v) AR2_applyAllFireModes(v) end)
    R(sec, "Hitbox Expander", "opt_hbe", AR2_CFG.weapon, "hbeOn", "Combat",
        function(v) AR2_hbeSetEnabled(v) end)
    S(sec, "HBE Size", "opt_hbe_size", AR2_CFG.weapon, "hbeSize", 1, 12, "Combat",
        nil, function(v) AR2_hbeUpdateSizes() end)
    R(sec, "Always Suppressed", "opt_suppressed", AR2_CFG.weapon, "alwaysSuppressed", "Combat",
        function(v) AR2_setAlwaysSuppressed(v) end)
    R(sec, "Vehicle Equip", "opt_veh_equip", AR2_CFG.weapon, "vehicleEquip", "Combat",
        function(v) AR2_setVehicleEquip(v) end)
end

do
    local sec = patchSection(TABS["Combat"]:AddSection({ Name = "Crosshair" }))
    registerSectionLabel(sec, "opt_crosshair")
    R(sec, "Enable Crosshair", "opt_crosshair", AR2_CFG.crosshair, "enabled", "Combat",
        function(v) AR2_toggleCrosshair(v) end)
D(sec, "Shape", "opt_ch_shape", AR2_CFG.crosshair, "shape",
    { "Cross", "Dot", "Circle", "T", "Square" }, "Combat")
    S(sec, "Size", "opt_ch_size", AR2_CFG.crosshair, "size", 3, 60, "Combat")
    S(sec, "Gap", "opt_ch_gap", AR2_CFG.crosshair, "gap", 0, 30, "Combat")
    S(sec, "Thickness", "opt_ch_thickness", AR2_CFG.crosshair, "thickness", 1, 6, "Combat")
    S(sec, "Hue", "opt_ch_hue", AR2_CFG.crosshair, "hue", 0, 360, "Combat")
    R(sec, "Rainbow", "opt_ch_rainbow", AR2_CFG.crosshair, "rainbow", "Combat")
end

-- ============================================================
-- 64. MOVEMENT
-- ============================================================
do
    local sec = patchSection(TABS["Movement"]:AddSection({ Name = t("sec_movement_player") }))
    registerSectionLabel(sec, "sec_movement_player")
    R(sec, "Speed Hack",       "opt_speed_hack",       AR2_CFG.speed,    "on", "Movement", function(v) if v then AR2_speed.start() else AR2_speed.stop() end end, "speedHack")
    S(sec, "Speed %", "opt_speed_pct", AR2_CFG.speed, "pct", 100, 400, "Movement")
    R(sec, "Collide Check", "opt_speed_collide", AR2_CFG.speed, "collide", "Movement")
    R(sec, "Pulse", "opt_speed_pulse", AR2_CFG.speed, "pulse", "Movement")
    S(sec, "Pulse On (s)", "opt_speed_pulse_on", AR2_CFG.speed, "pulseOn", 5, 100, "Movement")
    S(sec, "Pulse Off (s)", "opt_speed_pulse_off", AR2_CFG.speed, "pulseOff", 5, 100, "Movement")
    R(sec, "Adaptive", "opt_speed_adaptive", AR2_CFG.speed, "adaptive", "Movement")
    S(sec, "Min %", "opt_speed_min_pct", AR2_CFG.speed, "minPct", 100, 200, "Movement")
    R(sec, "Airborne", "opt_speed_airborne", AR2_CFG.speed, "airborne", "Movement")
    R(sec, "Spoof MoveState", "opt_speed_spoof", AR2_CFG.speed, "spoof", "Movement")
    D(sec, "Spoof As", "opt_speed_spoof_as", AR2_CFG.speed, "spoofAs",
        { "Falling", "Running", "RunningNoPhysics", "Climbing", "Swimming" }, "Movement")
    R(sec, "Infinite Jump",    "opt_infinite_jump",    AR2_CFG.misc,     "infJump", "Movement", function(v) if v then AR2_startInfJump() else AR2_stopInfJump() end end, "infJump")
    R(sec, "Noclip",           "opt_noclip",           AR2_CFG.misc,     "noclip", "Movement", function(v) AR2_toggleNoclip(v) end, "noclip")
    S(sec, "Noclip Speed", "opt_noclip_speed", AR2_CFG.misc, "noclipSpeed", 0, 500, "Movement")
    R(sec, "Bunny Hop",        "opt_bunny_hop",        AR2_CFG.misc,     "bhop", "Movement", function(v) if v then AR2_startBhop() else AR2_stopBhop() end end, "bhop")
    S(sec, "Hop Height", "opt_bhop_height", AR2_CFG.misc, "bhopHeight", 5, 50, "Movement")
    R(sec, "TP Dash",          "opt_tp_dash",          AR2_CFG.misc,     "tpDash", "Movement", function(v) if v then AR2_startTpDash() else AR2_stopTpDash() end end, "tpDash")
    S(sec, "TP Dash Studs", "opt_tp_dash_studs", AR2_CFG.misc, "tpDashStuds", 5, 200, "Movement")
    S(sec, "TP Dash Interval (s)", "opt_tp_dash_int", AR2_CFG.misc, "tpDashInterval", 0.1, 5, "Movement", 2)
    S(sec, "TP Dash Vertical", "opt_tp_dash_vert", AR2_CFG.misc, "tpDashVert", 0, 5, "Movement", 2)
end

do
    local sec = patchSection(TABS["Movement"]:AddSection({ Name = t("sec_fly"), Position = "right" }))
    registerSectionLabel(sec, "sec_fly")
    R(sec, "Fly",              "opt_fly",              AR2_CFG.misc,     "fly", "Movement", function(v) AR2_setFly(v) end, "fly")
    S(sec, "Fly Speed", "opt_fly_speed", AR2_CFG.misc, "flySpeedPct", 10, 1000, "Movement",
        nil, function(v) AR2_flySpeed = v end)
    R(sec, "Car Fly",          "opt_car_fly",          AR2_CFG.car,      "fly", "Movement", function(v) AR2_carFlySetEnabled(v) end, "carFly")
    S(sec, "Car Fly Speed", "opt_car_fly_speed", AR2_CFG.car, "flySpeed", 10, 200, "Movement")
    R(sec, "Spin Bot",         "opt_spin_bot",         AR2_CFG.misc,     "spinOn", "Movement", function(v) AR2_setSpin(v) end, "spinBot")
    S(sec, "Spin Speed", "opt_spin_speed", AR2_CFG.misc, "spinSpeed", 1, 60, "Movement")
end

-- ============================================================
-- 65. CARS
-- ============================================================
do
    local sec = patchSection(TABS["Cars"]:AddSection({ Name = t("sec_cars_tuning") }))
    registerSectionLabel(sec, "sec_cars_tuning")
    R(sec, "Infinite Fuel", "opt_inf_fuel", AR2_CFG.car, "fuel", "Cars",
        function() AR2_carMods.apply() end)
    R(sec, "Boost",   "opt_boost",    AR2_CFG.car, "boost", "Cars", function() AR2_carMods.apply() end)
    R(sec, "Godmode", "opt_godmode",  AR2_CFG.car, "god",   "Cars", function() AR2_carMods.apply() end)
    R(sec, "Max Traction", "opt_max_traction", AR2_CFG.car, "maxTraction", "Cars",
        function() AR2_carMods.apply() end)
    R(sec, "All Cars", "opt_all_cars", AR2_CFG.car, "allCars", "Cars",
        function() AR2_carMods.apply() end)
    S(sec, "Speed",  "opt_car_speed",  AR2_CFG.car, "speed",  44, 400, "Cars")
    S(sec, "Torque", "opt_car_torque", AR2_CFG.car, "torque", 1,  10,  "Cars")
end

do
    local sec = patchSection(TABS["Cars"]:AddSection({ Name = t("sec_cars_noclip"), Position = "right" }))
    registerSectionLabel(sec, "sec_cars_noclip")
R(sec, "Vehicle Noclip",   "opt_vehicle_noclip",   AR2_CFG.misc,     "carNoclip", "Cars", function(v) if v then local ok = AR2_carNoclipSetEnabled(true, true) if not ok then AR2_CFG.misc.carNoclip = false AR2_notify("Precisa estar sentado num veículo", 3) return end AR2_carNoclipDriveStart() else AR2_carNoclipDriveStop() AR2_carNoclipSetEnabled(false) task.spawn(function() task.wait(AR2_CFG.car.noclipEjectDelay or 0.15) pcall(AR2_ejectFromVehicle) end) end end, "vehicleNoclip")
    S(sec, "Noclip Speed", "opt_noclip_speed_car", AR2_CFG.car, "noclipSpeed", 10, 1000, "Cars")
    S(sec, "Noclip Eject Delay (s)", "opt_noclip_eject", AR2_CFG.car, "noclipEjectDelay", 0, 2, "Cars", 2)
S(sec, "Noclip TP Speed", "opt_noclip_tp_speed", AR2_CFG.car, "noclipTPSpeed", 50, 2000, "Cars")
S(sec, "Noclip TP Vertical", "opt_noclip_tp_vert", AR2_CFG.car, "noclipTPVertOffset", 0, 50, "Cars")
S(sec, "Noclip TP Max Time (s)", "opt_noclip_tp_max", AR2_CFG.car, "noclipTPMaxSeconds", 2, 60, "Cars", 2)
    R(sec, "Boat Mode", "opt_boat_mode", AR2_CFG.car, "boatMode", "Cars",
        function(v) AR2_setBoatMode(v) end)
    R(sec, "Remove Drag", "opt_remove_drag", AR2_CFG.car, "removeDrag", "Cars",
        function(v) AR2_setRemoveDrag(v) end)
    R(sec, "Full Steer", "opt_full_steer", AR2_CFG.car, "fullSteer", "Cars",
        function(v) AR2_setFullSteer(v) end)
R(sec, "Car TP Dash", "opt_car_tp_dash", AR2_CFG.car, "tpDash", "Cars",
    function(v) if v then AR2_startCarDash() else AR2_stopCarDash() end end)
S(sec, "Car Dash Studs", "opt_car_tp_studs", AR2_CFG.car, "tpDashStuds", 5, 300, "Cars")
S(sec, "Car Dash Interval (s)", "opt_car_tp_int", AR2_CFG.car, "tpDashInterval", 0.1, 5, "Cars", 2)
S(sec, "Car Dash Vertical", "opt_car_tp_vert", AR2_CFG.car, "tpDashVert", 0, 5, "Cars", 2)
end

-- ============================================================
-- 66. WORLD
-- ============================================================
do
    local sec = patchSection(TABS["World"]:AddSection({ Name = t("sec_lighting") }))
    registerSectionLabel(sec, "sec_lighting")
    R(sec, "Fullbright", "opt_fullbright", AR2_CFG.visuals, "fullbright", "World",
        function() AR2_visuals.apply() end)
    R(sec, "No Fog", "opt_no_fog", AR2_CFG.visuals, "nofog", "World",
        function() AR2_visuals.apply() end)
    S(sec, "Brightness", "opt_brightness", AR2_CFG.visuals, "brightness", 0, 10, "World",
        nil, function() AR2_visuals.apply() end)
    S(sec, "Time", "opt_time", AR2_CFG.visuals, "time", 0, 24, "World",
        nil, function() AR2_visuals.apply() end)
    R(sec, "Remove Shadows", "opt_remove_shadows", AR2_CFG.misc, "removeShadows", "World",
        function(v) AR2_setRemoveShadows(v) end)
    R(sec, "Remove Textures", "opt_remove_textures", AR2_CFG.misc, "removeTextures", "World",
        function(v) AR2_toggleRemoveTextures(v) end)
    R(sec, "Remove Leaves", "opt_remove_leaves", AR2_CFG.misc, "removeLeaves", "World",
        function(v) AR2_toggleRemoveLeaves(v) end)
    R(sec, "Remove Bushes", "opt_remove_bushes", AR2_CFG.misc, "removeBushes", "World",
        function(v) AR2_toggleRemoveBushes(v) end)
end

do
    local sec = patchSection(TABS["World"]:AddSection({ Name = t("sec_camera"), Position = "right" }))
    registerSectionLabel(sec, "sec_camera")
    R(sec, "FOV Zoom (hold RMB)", "opt_fov_zoom", AR2_CFG.misc, "fovZoom", "World",
        function(v) AR2_toggleFOVZoom(v) end)
    S(sec, "Zoom FOV", "opt_zoom_fov", AR2_CFG.misc, "zoomFOV", 10, 100, "World",
        nil, function(v) AR2_zoomFOV = v end)
    R(sec, "Distance Zoom", "opt_dist_zoom", AR2_CFG.misc, "distZoomEnabled", "World",
        function(v) AR2_toggleDistZoomEnabled(v) end)
    S(sec, "Normal Dist", "opt_normal_dist", AR2_CFG.misc, "normalDist", 3, 30, "World",
        nil, function(v) AR2_setNormalDistance(v) end)
    S(sec, "Zoomed Dist", "opt_zoomed_dist", AR2_CFG.misc, "zoomedDist", 1, 10, "World",
        nil, function(v) AR2_setZoomedDistance(v) end)
    R(sec, "Third Person ADS", "opt_3p_ads", AR2_CFG.misc.thirdPersonADS, "enabled", "World",
        function(v) AR2_set3pAds(v) end)
    R(sec, "Remove Sun Rays", "opt_remove_sunrays", AR2_CFG.visuals, "removeSunRays", "World",
        function(v) AR2_setRemoveSunRays(v) end)
    R(sec, "Custom Ambient", "opt_custom_ambient", AR2_CFG.visuals, "customAmbient", "World",
        function(v) AR2_setCustomAmbient(v) end)
end

do
    local sec = patchSection(TABS["World"]:AddSection({ Name = t("sec_clouds"), Position = "right" }))
    registerSectionLabel(sec, "sec_clouds")
    R(sec, "Enable Clouds", "opt_clouds_on", AR2_CFG.visuals, "cloudsEnabled", "World",
        function(v) AR2_setClouds(v, AR2_CFG.visuals.modifyClouds, AR2_CFG.visuals.cloudsColor, AR2_CFG.visuals.cloudsCover, AR2_CFG.visuals.cloudsDensity) end)
    R(sec, "Modify Clouds", "opt_clouds_modify", AR2_CFG.visuals, "modifyClouds", "World",
        function(v) AR2_setClouds(AR2_CFG.visuals.cloudsEnabled, v, AR2_CFG.visuals.cloudsColor, AR2_CFG.visuals.cloudsCover, AR2_CFG.visuals.cloudsDensity) end)
    C(sec, "Cloud Color", "opt_clouds_color", AR2_CFG.visuals, "cloudsColor", "World",
        function(v) AR2_setClouds(AR2_CFG.visuals.cloudsEnabled, AR2_CFG.visuals.modifyClouds, v, AR2_CFG.visuals.cloudsCover, AR2_CFG.visuals.cloudsDensity) end)
    S(sec, "Cloud Cover", "opt_clouds_cover", AR2_CFG.visuals, "cloudsCover", 0, 1, "World", 2,
        function(v) AR2_setClouds(AR2_CFG.visuals.cloudsEnabled, AR2_CFG.visuals.modifyClouds, AR2_CFG.visuals.cloudsColor, v, AR2_CFG.visuals.cloudsDensity) end)
    S(sec, "Cloud Density", "opt_clouds_density", AR2_CFG.visuals, "cloudsDensity", 0, 1, "World", 2,
        function(v) AR2_setClouds(AR2_CFG.visuals.cloudsEnabled, AR2_CFG.visuals.modifyClouds, AR2_CFG.visuals.cloudsColor, AR2_CFG.visuals.cloudsCover, v) end)
end

do
    local sec = patchSection(TABS["World"]:AddSection({ Name = t("sec_cc") }))
    registerSectionLabel(sec, "sec_cc")
    R(sec, "Enable CC", "opt_cc_on", AR2_CFG.visuals, "colorCorrectionEnabled", "World",
        function(v) AR2_setColorCorrection(v, AR2_CFG.visuals.ccSaturation, AR2_CFG.visuals.ccContrast, AR2_CFG.visuals.ccBrightness) end)
    S(sec, "Saturation", "opt_cc_sat", AR2_CFG.visuals, "ccSaturation", 0, 3, "World", 2,
        function(v) AR2_setColorCorrection(AR2_CFG.visuals.colorCorrectionEnabled, v, AR2_CFG.visuals.ccContrast, AR2_CFG.visuals.ccBrightness) end)
    S(sec, "Contrast", "opt_cc_cont", AR2_CFG.visuals, "ccContrast", 0, 2, "World", 2,
        function(v) AR2_setColorCorrection(AR2_CFG.visuals.colorCorrectionEnabled, AR2_CFG.visuals.ccSaturation, v, AR2_CFG.visuals.ccBrightness) end)
    S(sec, "Brightness", "opt_cc_bright", AR2_CFG.visuals, "ccBrightness", 0, 3, "World", 2,
        function(v) AR2_setColorCorrection(AR2_CFG.visuals.colorCorrectionEnabled, AR2_CFG.visuals.ccSaturation, AR2_CFG.visuals.ccContrast, v) end)
end

-- ============================================================
-- 67. RADAR
-- ============================================================
do
    local sec = patchSection(TABS["Radar"]:AddSection({ Name = t("sec_radar_main") }))
    registerSectionLabel(sec, "sec_radar_main")
    R(sec, "Enabled", "opt_radar_enabled", AR2_CFG.radar, "on", "Radar",
        function(v) if v then AR2_radar.start() else AR2_radar.stop() end end)
    R(sec, "Names",    "opt_radar_names", AR2_CFG.radar, "names",    "Radar")
    R(sec, "Distance", "opt_radar_dist",  AR2_CFG.radar, "dist",     "Radar")
    R(sec, "Include Self", "opt_radar_self", AR2_CFG.radar, "localToo", "Radar")
    R(sec, "Armed Color",  "opt_radar_armed", AR2_CFG.radar, "armedColor", "Radar")
    R(sec, "Show Weapon", "opt_radar_weapon", AR2_CFG.radar, "weapon", "Radar")
end

do
    local sec = patchSection(TABS["Radar"]:AddSection({ Name = t("sec_minimap"), Position = "right" }))
    registerSectionLabel(sec, "sec_minimap")
    R(sec, "Minimap", "opt_minimap", AR2_CFG.minimap, "on", "Radar",
        function(v) AR2_minimapEnabled = v AR2_minimapUpdate() AR2_updateMinimap() end)
    S(sec, "Minimap Size", "opt_minimap_size", AR2_CFG.minimap, "size", 100, 600, "Radar",
        nil, function() AR2_updateMinimap() end)
        S(sec, "Minimap X", "opt_minimap_x", AR2_CFG.minimap, "x", 0, 2000, "Radar",
    nil, function() AR2_updateMinimap() end)
S(sec, "Minimap Y", "opt_minimap_y", AR2_CFG.minimap, "y", 0, 2000, "Radar",
    nil, function() AR2_updateMinimap() end)
    R(sec, "Radar Vehicles", "opt_radar_vehicles", AR2_CFG.radar.vehicles, "on", "Radar")
    R(sec, "Radar Corpses",  "opt_radar_corpses",  AR2_CFG.radar.corpses,  "on", "Radar")
end

-- ============================================================
-- 68. TELEPORT
-- ============================================================
do
    local sec = patchSection(TABS["Teleport"]:AddSection({ Name = t("sec_tp_player") }))
    registerSectionLabel(sec, "sec_tp_player")
    S(sec, "Behind", "opt_behind", AR2_CFG.tp, "behind", -30, 30, "Teleport")
    S(sec, "Above",  "opt_above",  AR2_CFG.tp, "above",  -20, 60, "Teleport")
    S(sec, "Side",   "opt_side",   AR2_CFG.tp, "side",   -30, 30, "Teleport")
    sec:AddButton({ Name = t("opt_tp_to_target"), Callback = function() AR2_tp.toPlayer() end })
    sec:AddButton({ Name = t("opt_open_map"),     Callback = function() AR2_tp.toggleMap(true) end })
    R(sec, "Respawn TP", "opt_respawn_tp", AR2_CFG.tp, "respawnTP", "Teleport",
        function(v) AR2_toggleRespawnTP(v) end)
end

-- ============================================================
-- 69. DIVERSOS
-- ============================================================
do
    local sec = patchSection(TABS["Diversos"]:AddSection({ Name = t("sec_freeze") }))
    registerSectionLabel(sec, "sec_freeze")
    R(sec, "Freeze Zombies", "opt_freeze_zombies", AR2_CFG.misc, "freeze", "Diversos",
        function(v) if v then AR2_freeze.start() else AR2_freeze.stop() end end)
    R(sec, "Whole Server", "opt_whole_server", AR2_CFG.misc, "freezeAll", "Diversos")
    R(sec, "Anchor", "opt_freeze_anchor", AR2_CFG.misc, "freezeAnchor", "Diversos")
    S(sec, "Radius", "opt_radius", AR2_CFG.misc, "freezeRadius", 50, 2000, "Diversos")
end

do
    local sec = patchSection(TABS["Diversos"]:AddSection({ Name = t("sec_camera"), Position = "right" }))
    registerSectionLabel(sec, "sec_camera")
    R(sec, "Anti-Aim",         "opt_anti_aim",         AR2_CFG.misc.antiAim, "enabled", "Diversos", function(v) AR2_toggleAntiAim(v) end, "antiAim")
    R(sec, "Jump Circle", "opt_jump_circle", AR2_CFG.misc.jumpCircle, "enabled", "Diversos",
        function(v) AR2_setJumpCircle(v) end)
    R(sec, "Self Backtrack", "opt_self_backtrack", AR2_CFG.misc.selfBacktrack, "enabled", "Diversos",
        function(v) AR2_setSelfBacktrack(v) end)
    R(sec, "Instant Interact", "opt_instant_interact", AR2_CFG.misc, "instantInteract", "Diversos",
        function(v) AR2_setInstantInteract(v) end)
    R(sec, "Player Jesus", "opt_player_jesus", AR2_CFG.misc, "playerJesus", "Diversos",
        function(v) AR2_setPlayerJesus(v) end)
    R(sec, "Zombie Circle", "opt_zombie_circle", AR2_CFG.misc.zombieCircle, "enabled", "Diversos",
        function(v) AR2_setZombieCircle(v) end)
    R(sec, "Hide Player", "opt_hide_player", AR2_CFG.misc, "hidePlayer", "Diversos",
        function(v) AR2_toggleHidePlayer(v) end)
    R(sec, "Hide Gun", "opt_hide_gun", AR2_CFG.misc, "hideGun", "Diversos",
        function(v) AR2_toggleHideGun(v) end)
    R(sec, "Anti-Fall Stun", "opt_anti_fall", AR2_CFG.misc, "antiFallStun", "Diversos",
        function(v) AR2_setAntiFallStun(v) end)
end

do
    local sec = patchSection(TABS["Diversos"]:AddSection({ Name = t("sec_outlines") }))
    registerSectionLabel(sec, "sec_outlines")
    R(sec, "Enabled", "opt_charm", AR2_CFG.misc, "charm", "Diversos",
        function(v) AR2_toggleCharm(v) end)
    D(sec, "Mode", "opt_charm_mode", AR2_CFG.misc, "charmMode",
        { "Color", "BlackWhite" }, "Diversos",
        function(v) AR2_setCharmMode(v) end)
    S(sec, "Hue", "opt_charm_hue", AR2_CFG.misc, "charmHue", 0, 360, "Diversos",
        nil, function(v) AR2_setCharmHue(v) end)
    S(sec, "Gray", "opt_charm_gray", AR2_CFG.misc, "charmGray", 0, 100, "Diversos",
        nil, function(v) AR2_setCharmGray(v) end)
    R(sec, "Rainbow", "opt_charm_rainbow", AR2_CFG.misc, "charmRainbow", "Diversos",
        function(v) AR2_toggleCharmRainbow(v) end)
    R(sec, "Global Outline", "opt_charm_global", AR2_CFG.misc, "globalCharm", "Diversos",
        function() AR2_applyCharm(AR2_LocalPlayer.Character) end)
end

do
    local sec = patchSection(TABS["Diversos"]:AddSection({ Name = t("sec_personal"), Position = "right" }))
    registerSectionLabel(sec, "sec_personal")

    -- Character
    R(sec, "Character Appearance", "opt_pc_char_on", AR2_CFG.misc, "personalCharOn", "Diversos",
        function(v) AR2_setPersonalCharacter(v, AR2_CFG.misc.personalCharMode, AR2_CFG.misc.personalCharHue, AR2_CFG.misc.personalCharGray, AR2_CFG.misc.personalCharMaterial, AR2_CFG.misc.personalCharRainbow) end)
    D(sec, "Char Mode", "opt_pc_char_mode", AR2_CFG.misc, "personalCharMode",
        { "Color", "BlackWhite" }, "Diversos",
        function(v) if AR2_CFG.misc.personalCharOn then AR2_setPersonalCharacter(true, v, AR2_CFG.misc.personalCharHue, AR2_CFG.misc.personalCharGray, AR2_CFG.misc.personalCharMaterial, AR2_CFG.misc.personalCharRainbow) end end)
    S(sec, "Char Hue", "opt_pc_char_hue", AR2_CFG.misc, "personalCharHue", 0, 360, "Diversos",
        nil, function(v) if AR2_CFG.misc.personalCharOn then AR2_setPersonalCharacter(true, AR2_CFG.misc.personalCharMode, v, AR2_CFG.misc.personalCharGray, AR2_CFG.misc.personalCharMaterial, AR2_CFG.misc.personalCharRainbow) end end)
    S(sec, "Char Gray", "opt_pc_char_gray", AR2_CFG.misc, "personalCharGray", 0, 100, "Diversos",
        nil, function(v) if AR2_CFG.misc.personalCharOn then AR2_setPersonalCharacter(true, AR2_CFG.misc.personalCharMode, AR2_CFG.misc.personalCharHue, v, AR2_CFG.misc.personalCharMaterial, AR2_CFG.misc.personalCharRainbow) end end)
    D(sec, "Char Material", "opt_pc_char_mat", AR2_CFG.misc, "personalCharMaterial",
        { "ForceField", "Neon" }, "Diversos",
        function(v) if AR2_CFG.misc.personalCharOn then AR2_setPersonalCharacter(true, AR2_CFG.misc.personalCharMode, AR2_CFG.misc.personalCharHue, AR2_CFG.misc.personalCharGray, v, AR2_CFG.misc.personalCharRainbow) end end)
    R(sec, "Char Rainbow", "opt_pc_char_rb", AR2_CFG.misc, "personalCharRainbow", "Diversos",
        function(v) if AR2_CFG.misc.personalCharOn then AR2_setPersonalCharacter(true, AR2_CFG.misc.personalCharMode, AR2_CFG.misc.personalCharHue, AR2_CFG.misc.personalCharGray, AR2_CFG.misc.personalCharMaterial, v) end end)

    -- Gun
    R(sec, "Gun Appearance", "opt_pg_gun_on", AR2_CFG.misc, "personalGunOn", "Diversos",
        function(v) AR2_setPersonalGun(v, AR2_CFG.misc.personalGunMode, AR2_CFG.misc.personalGunHue, AR2_CFG.misc.personalGunGray, AR2_CFG.misc.personalGunMaterial, AR2_CFG.misc.personalGunRainbow) end)
    D(sec, "Gun Mode", "opt_pg_gun_mode", AR2_CFG.misc, "personalGunMode",
        { "Color", "BlackWhite" }, "Diversos",
        function(v) if AR2_CFG.misc.personalGunOn then AR2_setPersonalGun(true, v, AR2_CFG.misc.personalGunHue, AR2_CFG.misc.personalGunGray, AR2_CFG.misc.personalGunMaterial, AR2_CFG.misc.personalGunRainbow) end end)
    S(sec, "Gun Hue", "opt_pg_gun_hue", AR2_CFG.misc, "personalGunHue", 0, 360, "Diversos",
        nil, function(v) if AR2_CFG.misc.personalGunOn then AR2_setPersonalGun(true, AR2_CFG.misc.personalGunMode, v, AR2_CFG.misc.personalGunGray, AR2_CFG.misc.personalGunMaterial, AR2_CFG.misc.personalGunRainbow) end end)
    S(sec, "Gun Gray", "opt_pg_gun_gray", AR2_CFG.misc, "personalGunGray", 0, 100, "Diversos",
        nil, function(v) if AR2_CFG.misc.personalGunOn then AR2_setPersonalGun(true, AR2_CFG.misc.personalGunMode, AR2_CFG.misc.personalGunHue, v, AR2_CFG.misc.personalGunMaterial, AR2_CFG.misc.personalGunRainbow) end end)
    D(sec, "Gun Material", "opt_pg_gun_mat", AR2_CFG.misc, "personalGunMaterial",
        { "Neon", "ForceField" }, "Diversos",
        function(v) if AR2_CFG.misc.personalGunOn then AR2_setPersonalGun(true, AR2_CFG.misc.personalGunMode, AR2_CFG.misc.personalGunHue, AR2_CFG.misc.personalGunGray, v, AR2_CFG.misc.personalGunRainbow) end end)
    R(sec, "Gun Rainbow", "opt_pg_gun_rb", AR2_CFG.misc, "personalGunRainbow", "Diversos",
        function(v) if AR2_CFG.misc.personalGunOn then AR2_setPersonalGun(true, AR2_CFG.misc.personalGunMode, AR2_CFG.misc.personalGunHue, AR2_CFG.misc.personalGunGray, AR2_CFG.misc.personalGunMaterial, v) end end)
end

-- ============================================================
-- 70. COSMETICS
-- ============================================================
do
    local sec = patchSection(TABS["Cosmetics"]:AddSection({ Name = t("sec_unlocker") }))
    registerSectionLabel(sec, "sec_unlocker")
    R(sec, "Enable Selection", "opt_enable_selection", AR2_CFG.misc, "cosUnlock", "Cosmetics",
        function(v) AR2_cosSetEnabled(v) end)
    sec:AddButton({ Name = t("opt_remove_all_cos"), Callback = function()
        AR2_cosRemoveAll()
        AR2_notify("Cosméticos removidos", 3)
    end })
end

-- ============================================================
-- 71. CONFIG
-- ============================================================
do
    local sec = patchSection(TABS["Config"]:AddSection({ Name = t("sec_language") }))
    registerSectionLabel(sec, "sec_language")
    local langList = {}
    for k, _ in pairs(LOCALES) do table.insert(langList, k) end
    table.sort(langList)

    sec:AddLabel(t("opt_language")):AddDropdown({
    Values = langList,
    Default = CURRENT_LANG,
    Callback = function(chosen)
        if not LOCALES[chosen] then return end
        if getgenv().TetescrHUB_applyLocale then
            pcall(getgenv().TetescrHUB_applyLocale, chosen)
        end
        if getgenv().TetescrHUB_saveLocale then
            pcall(getgenv().TetescrHUB_saveLocale, chosen)
        end
    end,
})
end

do
    local sec = patchSection(TABS["Config"]:AddSection({ Name = t("sec_window") }))
    registerSectionLabel(sec, "sec_window")
    
    -- save: input de nome
    local saveName = "meu_config"
    sec:AddLabel("Config name"):AddTextInput({
        Default = "meu_config",
        Placeholder = "nome da config",
        Callback = function(v) saveName = v end,
    })
    
    sec:AddButton({ Name = "Save Config", Callback = function()
         local ok, err = AR2_saveConfig(saveName)
        if ok then AR2_notify("Salvo: "..saveName, 3) else AR2_notify("Falha: "..tostring(err), 4) end
    end })
    
    sec:AddButton({ Name = "Load Config", Callback = function()
        local ok, err = AR2_loadConfigByName(saveName)
        if ok then 
            if AR2_refreshUI then AR2_refreshUI() end
            AR2_notify("Carregado: "..saveName, 3) 
        else AR2_notify("Falha: "..tostring(err), 4) end
    end })
    
    sec:AddButton({ Name = t("opt_reset_defaults"), Callback = function()
        AR2_resetConfig()
        if AR2_refreshUI then AR2_refreshUI() end
        AR2_notify("Resetado", 3)
    end })
end

do
    local sec = patchSection(TABS["Config"]:AddSection({ Name = t("sec_keybinds") }))
    registerSectionLabel(sec, "sec_keybinds")
    local elem = sec:AddKeybindRow({
        Name = "Open/Close UI",
        TransKey = "opt_toggle_ui",
        Default = "RightShift",
        Accent = CAT["Config"],
        Callback = function(v)
            AR2_CFG.misc.uiToggleKey = v
        end,
    })
    trackRow(elem,
        function() return AR2_CFG.misc.uiToggleKey or "RightShift" end,
        function(v) if elem and elem.SetValue then elem.SetValue(v) end end)
end

-- ============================================================
-- 72. APLICA LOCALE + KEYBIND DE TOGGLE UI
-- ============================================================
applyLocale(CURRENT_LANG)

AR2_UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.UserInputType ~= Enum.UserInputType.Keyboard then return end
    local keyName = input.KeyCode.Name
    local toggleKey = AR2_normalizeKey(AR2_CFG.misc.uiToggleKey or "RightShift")
    if toggleKey == keyName then
        AR2_toggleUI()
    end
end)

-- ============================================================
-- 73. MOSTRA SCREENGUI E SINCRONIZA
-- ============================================================
task.wait(0.4)
if NL.ScreenGui then
    NL.ScreenGui.Enabled = true
end
if AR2_buildMinimizeButton then pcall(AR2_buildMinimizeButton) end
if AR2_refreshUI then pcall(AR2_refreshUI) end

-- ============================================================
-- FIM DO BLOCO E
-- ============================================================
print("[Tetescr hub] Bloco E carregado — UI montada")
print("[Tetescr hub] janela:", tostring(win and "ok" or "nil"), "— idioma:", CURRENT_LANG)
print("[Tetescr hub] tecla de toggle UI:", AR2_CFG.misc.uiToggleKey or "RightShift")
