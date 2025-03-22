local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local playerGui = player:WaitForChild("PlayerGui")
local hotbar = playerGui:FindFirstChild("Hotbar")
local backpack = hotbar:FindFirstChild("Backpack")
local hotbarFrame = backpack:FindFirstChild("Hotbar")

-- Define Replacement Animations with IDs (No sound IDs)
local replacementAnimations = {
    ["10468665991"] = {id = "rbxassetid://18896127525", speed = 2},  -- Sonic Strike (Punch)
    ["10466974800"] = {id = "rbxassetid://12273188754", speed = 1},  -- Hyper Dash (Stronger Punch)
    ["10471336737"] = {id = "rbxassetid://13294790250", speed = 1},  -- Sonic Boom (Energy Blast)
    ["12510170988"] = {id = "rbxassetid://12463072679", speed = 1},  -- Speed Breaker (Power Slam)

    -- New Animations (m1, m2, m3, m4) without Sound IDs
    ["10469493270"] = {id = "rbxassetid://17889458563", speed = 1.3},  -- m1
    ["10469630950"] = {id = "rbxassetid://17889461810", speed = 1.3},  -- m2
    ["10469639222"] = {id = "rbxassetid://17889471098", speed = 1.3},  -- m3
    ["10469643643"] = {id = "rbxassetid://17889290569", speed = 1.3},  -- m4

    -- New FDash Animation
    ["10479335397"] = {id = "rbxassetid://132259592388175", speed = 1},  -- FDash

    -- Downslam Animation (added here)
    ["10470104242"] = {id = "rbxassetid://17858997926", speed = 1},  -- Downslam
}

-- Ultimate Animations (added to the script)
local ultimateAnimations = {
    ["11343318134"] = {id = "rbxassetid://15123665491", speed = 1.6},  -- Ultimate Animation 1
    ["11365563255"] = {id = "rbxassetid://15520132233", speed = 0.5},  -- Ultimate Animation 2
    ["12983333733"] = {id = "rbxassetid://18896229321", speed = 1.2},  -- Ultimate Animation 3
    ["13927612951"] = {id = "rbxassetid://136370737633649", speed = 0.5},  -- Ultimate Animation 4
    ["12447707844"] = {id = "rbxassetid://15957361339", speed = 2},  -- Ultimate Activation Animation
}

-- Button Data for Moves in Hotbar
local buttonData = {
    {name = "1", text = "Sonic Strike"},
    {name = "2", text = "Hyper Dash"},
    {name = "3", text = "Sonic Boom"},
    {name = "4", text = "Speed Breaker"},
    -- Removed Downslam from here
}

-- Update the tool names on hotbar buttons
for _, data in pairs(buttonData) do
    local baseButton = hotbarFrame:FindFirstChild(data.name).Base
    local ToolName = baseButton.ToolName
    ToolName.Text = data.text
end

-- Function to handle animations (without sound)
local function onAnimationPlayed(animationTrack)
    local animationId = animationTrack.Animation.AnimationId:match("%d+")
    local replacement = replacementAnimations[animationId]
    local ultimate = ultimateAnimations[animationId]

    if replacement then
        -- Stop current animation and play replacement
        animationTrack:Stop()
        local newAnimation = Instance.new("Animation")
        newAnimation.AnimationId = replacement.id
        local newTrack = humanoid:LoadAnimation(newAnimation)
        newTrack:Play()
        newTrack:AdjustSpeed(replacement.speed)
    elseif ultimate then
        -- Stop current animation and play ultimate animation
        animationTrack:Stop()
        local newAnimation = Instance.new("Animation")
        newAnimation.AnimationId = ultimate.id
        local newTrack = humanoid:LoadAnimation(newAnimation)
        newTrack:Play()
        newTrack:AdjustSpeed(ultimate.speed)
    end
end

-- Connect AnimationPlayed event to the handler
humanoid.AnimationPlayed:Connect(onAnimationPlayed)

-- Reconnect the animation handler when character respawns
player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoid = newCharacter:WaitForChild("Humanoid")
    humanoid.AnimationPlayed:Connect(onAnimationPlayed)
end)

-- Function to morph the avatar to a different user (using their user ID)
local function morphToUser(userId)
    local targetPlayer = Players:GetPlayerByUserId(userId)
    if targetPlayer then
        local targetCharacter = targetPlayer.Character
        if targetCharacter then
            -- You can apply the necessary morph logic here
            character = targetCharacter:Clone()
            character.Parent = workspace
        end
    else
        warn("Player with UserId " .. userId .. " not found.")
    end
end

-- Call morphToUser with the specific userId
morphToUser(3461389772)

-- Function to create and spin Glowing Rainbow Chaos Emeralds around the player
local function createGlowingRainbowChaosEmeralds()
    local chaosEmeralds = {}  -- To hold the Chaos Emeralds
    local radius = 5  -- Radius for the circular path around the player
    local emeraldCount = 7  -- Number of Chaos Emeralds

    -- Create 7 Chaos Emeralds with Rainbow Effect
    for i = 1, emeraldCount do
        local emerald = Instance.new("Part")
        emerald.Shape = Enum.PartType.Ball
        emerald.Size = Vector3.new(2, 2, 2)
        emerald.Material = Enum.Material.Neon  -- Change to Neon for a glowing effect
        emerald.Anchored = true
        emerald.CanCollide = false
        emerald.Parent = workspace

        -- Apply rainbow colors to each emerald
        local colorValue = Color3.fromHSV((i - 1) / emeraldCount, 1, 1)  -- Generate a rainbow color for each emerald
        emerald.Color = colorValue

        -- Add a PointLight to make the emeralds glow
        local pointLight = Instance.new("PointLight")
        pointLight.Parent = emerald
        pointLight.Range = 10  -- Set the light range
        pointLight.Brightness = 5  -- Set the light brightness to make them shine brighter
        pointLight.Color = colorValue  -- Match the light color with the emerald's color

        -- Optional: Add a subtle particle effect for a soft glow around the emeralds
        local particleEmitter = Instance.new("ParticleEmitter")
        particleEmitter.Texture = "rbxassetid://2511572178"  -- Glow effect texture (you can replace with any glow texture)
        particleEmitter.Size = NumberSequence.new(1)
        particleEmitter.Lifetime = NumberRange.new(0.5, 1)
        particleEmitter.Rate = 100
        particleEmitter.Speed = NumberRange.new(5, 10)
        
        -- Create a subtle glow particle effect that changes with the rainbow colors
        particleEmitter.Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, colorValue),
            ColorSequenceKeypoint.new(1, colorValue)
        })
        particleEmitter.Parent = emerald

        -- Add the emerald to the list
        table.insert(chaosEmeralds, emerald)
    end

    -- Update the position of the Chaos Emeralds to spin around the player
    game:GetService("RunService").Heartbeat:Connect(function()
        -- Get the current position of the player
        local playerPosition = character.HumanoidRootPart.Position
        -- Spin the Chaos Emeralds around the player
        for i, emerald in pairs(chaosEmeralds) do
            local angle = math.rad(i * 360 / emeraldCount) + tick()  -- Rotate based on time (tick) for continuous spin

            -- Calculate new position in a circular formation around the player
            local xPos = playerPosition.X + radius * math.cos(angle)
            local zPos = playerPosition.Z + radius * math.sin(angle)
            emerald.Position = Vector3.new(xPos, playerPosition.Y + 5, zPos)  -- Positioning just above the character
        end
    end)
end

-- Call the function to create the Glowing Rainbow Chaos Emeralds and make them spin around the player
createGlowingRainbowChaosEmeralds()
