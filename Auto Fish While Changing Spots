--It will fish until the spot gets unavailable then move on to the next, forever
--Required Plugins: Vnavmesh, Autohook and Teleporter. (Enable Auto Cast Line in Autohook)

--****************************************************
--*       MAKE SURE YOU HAVE A BAIT EQUIPPED         *
--*    SELECT BELOW THE EXPANSION YOU WANT TO FISH   *
--**************************************************** 

-- Change to "HW", "SB", "ShB", "EW", "DT", "Noscea", "Shroud" or "Thanalan"

local selectedMap = "EW"  


--*******************************************************************
--*                       SCRIPT STARTING                           *
--*******************************************************************


-- Coordenadas dos Nodes
local mapNodes = {
    HW = {
        {x = 159.53165, y = -91.30772, z = 205.79218},
        {x = 410.7847, y = -46.602413, z = -394.401},
        {x = 434.00143, y = -26.253891, z = -597.24133}
    },
    SB = {
        {x = 93.468216, y = -0.5, z = -533.2428},
        {x = -704.39343, y = -0.100000024, z = -437.23193},
        {x = 546.16156, y = -0.099999905, z = -506.03177}
    },
    ShB = {
        {x = 457.25854, y = 1.0189526, z = 255.7363},
        {x = 400.96274, y = 20.69755, z = 30.649504},
        {x = 613.3553, y = -0.6220832, z = 567.8177}
    },
    EW = {
        {x = 273.39072, y = -0.18860883, z = 755.61945},
        {x = -353.47308, y = -0.24561608, z = 561.1803},
        {x = -511.95007, y = -0.08991444, z = -11.119758}
    },
    DT = {
        {x = 24.086561, y = 0.8344071, z = -270.9346},
        {x = -186.18437, y = 109.86891, z = 170.67009},
        {x = 172.33582, y = 110.10605, z = 548.59564}
    },
    Noscea = {
        {x = -365.141, y = -35.220753, z = -435.4921},
        {x = -225.78525, y = -42.367718, z = -218.76549},
        {x = -299.63327, y = -42.29065, z = 715.6654}
    },
    Shroud = {
        {x = 534.295, y = 14.024188, z = 141.45811},
        {x = 528.034, y = 17.741676, z = 99.30884},
        {x = 621.98145, y = 21.665985, z = 116.08524}
    },
    Thanalan = {
        {x = -203.78053, y = 14.639931, z = -737.60394},
        {x = -150.77452, y = 14.515914, z = -734.2374},
        {x = -302.85773, y = 12.75757, z = -751.2393}
    }
}

-- Define o nome do teleporte e o número da zona com base no mapa selecionado
local teleportLocations = {
    HW = {name = "Tailfeather", zone = 398},
    SB = {name = "Onokoro", zone = 613},
    ShB = {name = "Stilltide", zone = 814},
    EW = {name = "Yedlihmad", zone = 957},
    DT = {name = "Ok'hanu", zone = 1188},
    Noscea = {name = "Aleport", zone = 138},
    Shroud = {name = "Quarrymill", zone = 153},
    Thanalan = {name = "Horizon", zone = 140}
}

local teleportLocation = teleportLocations[selectedMap] and teleportLocations[selectedMap].name or nil
local targetZone = teleportLocations[selectedMap] and teleportLocations[selectedMap].zone or nil
local nodes = mapNodes[selectedMap]

if not teleportLocation or not targetZone or not nodes then
    error("Expansion/Zone name is written incorrectly. Stop this script and try again.")
end

-- Para qual node ir
local currentNodeIndex = 1

-- Função para teletransportar para o mapa
function TeleportToLocation(location)
    yield("/tp " .. location)
    
    -- Aguarda enquanto está conjurando o teleporte
    repeat
        yield("/wait 1")
    until GetCharacterCondition(27) == true
    
    -- Aguarda enquanto o mapa está sendo carregado
    repeat
        yield("/wait 1")
    until GetCharacterCondition(27) == false and GetCharacterCondition(45) == true
    
    -- Aguarda até o mapa carregar completamente
    repeat
        yield("/wait 1")
    until GetCharacterCondition(45) == false
    
    -- Espera mais 1 segundo após o carregamento do mapa
    yield("/wait 1")
end

-- Ir até o node
function MoveToLocation(x, y, z)
    yield("/wait 1")
    yield("/gs change Fisher")
    yield("/wait 1")
    if not GetCharacterCondition(4) then
        yield('/gaction "mount roulette"')
    end
    yield("/wait 2")
    yield("/vnav flyto " .. x .. " " .. y .. " " .. z)
    repeat
        yield("/wait 1")
    until not PathIsRunning() and not PathfindInProgress()
    yield("/send Z")
    yield("/wait 3")
end

-- Verifica se o Node está ativo
function IsNodeActive()
    if (GetNodeText("_ScreenText", 11, 8) ==
        "The fish here have grown wise to your presence. You might have better luck in a new location..." or
        GetNodeText("_ScreenText", 11, 8) ==
        "The fish sense something amiss. Perhaps it is time to try another location.") then
        return false
    else
        return true
    end
end

-- Pescar
function Fishing()
    -- Verifica se o jogador já está na zona correta
    if not IsInZone(targetZone) then
        -- Teletransporta para o mapa selecionado antes de iniciar a pesca
        TeleportToLocation(teleportLocation)
    end
    
    while true do
        if not GetCharacterCondition(6) then
            local node = nodes[currentNodeIndex]
            MoveToLocation(node.x, node.y, node.z)

            yield("/ahon")
            yield("/ac Cast")
            
            repeat
                yield("/wait 1")
            until not IsNodeActive()

            yield("/wait 3")
            currentNodeIndex = currentNodeIndex + 1
            if currentNodeIndex > #nodes then
                currentNodeIndex = 1
            end
        end
    end
end

-- Inicia tudo
Fishing()
