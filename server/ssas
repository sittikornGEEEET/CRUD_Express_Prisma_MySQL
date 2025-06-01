if not game:IsLoaded() then
    task.delay(60, function()
        if NoShutdown then return end
        if not game:IsLoaded() then
            return game:Shutdown()
        end
        local Code = game:GetService'GuiService':GetErrorCode().Value
        if Code >= Enum.ConnectionError.DisconnectErrors.Value then
            return game:Shutdown()
        end
    end)
    game.Loaded:Wait()
end

local Request = (syn and syn.request) or request or (http and http.request) or http_request
local username = game.Players.LocalPlayer.Name
local IsGetKick = false
local TeleportService = game:GetService'TeleportService'
local InputService = game:GetService'UserInputService'
local HttpService = game:GetService'HttpService'
local RunService = game:GetService'RunService'
local GuiService = game:GetService'GuiService'
local Players = game:GetService'Players'
local LocalPlayer = Players.LocalPlayer if not LocalPlayer then repeat LocalPlayer = Players.LocalPlayer task.wait() until LocalPlayer end task.wait(0.5)
local UGS = UserSettings():GetService'UserGameSettings'

UGS.MasterVolume = 0
LocalPlayer.OnTeleport:Connect(function(State)
    if State == Enum.TeleportState.Started and Nexus.IsConnected then
        IsGetKick = true
    end
end)

game.StarterGui:SetCore("SendNotification", {
    Title = 'API SERVICES',
    Text = 'User : ' .. username,
    Duration = 25,
    Icon = 'rbxassetid://18976336309'
})

InputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.O then 
        RunService:Set3dRenderingEnabled(false)
    end
    if input.KeyCode == Enum.KeyCode.C then 
        RunService:Set3dRenderingEnabled(true)
    end
end)

settings().Rendering.QualityLevel = 1

local function Get_port(username)
    local url = "http://103.216.158.53:12345/getport?username=" .. username
    local response = Request({Url = url, Method = "GET"})
    if response and response.StatusCode == 200 and response.Body then
        local success, data = pcall(function() return game:GetService("HttpService"):JSONDecode(response.Body) end)
        if success and data.port then
            return data.port
        end
        warn("Failed to decode JSON or port not found in response")
    else
        warn("Request failed with status: " .. (response and response.StatusCode or "unknown"))
    end
    return nil
end

Port = Get_port(username) or 5000
print(Port)

local function checkForError()
    game:GetService("CoreGui").RobloxPromptGui.promptOverlay.ChildAdded:Connect(function(child)
        if child.Name == 'ErrorPrompt' and child:FindFirstChild('MessageArea') and child.MessageArea:FindFirstChild("ErrorFrame") then
            if game:GetService("GuiService"):GetErrorCode().Value then
                IsGetKick = true
            end
        end
    end)
    return IsGetKick
end

local function setOffline()
    local url = "http://127.0.0.1:" .. Port .. "/offline"
    local username = game.Players.LocalPlayer.Name
    local data = HttpService:JSONEncode({username = username})
    local requestData = {Url = url,Method = "POST", Headers = {["Content-Type"] = "application/json"},Body = data}
    local success, response = pcall(function()
        Request(requestData)  
    end)
    if not success then
        warn("Request failed: ", response)
    end
end

local function updateStatus()
    local url = "http://127.0.0.1:" .. Port .. "/"
    local username = game.Players.LocalPlayer.Name
    local data = HttpService:JSONEncode({username = username})
    local requestData = {Url = url,Method = "POST",Headers = {["Content-Type"] = "application/json"},Body = data}
    local success, response = pcall(function()
        return Request(requestData)
    end)
    if success and response and response.StatusCode == 200 then
        print("Status Updated API : " .. username)
    else
        warn("Failed Update API " .. (response and response.StatusCode or "Request failed"))
    end
end

task.spawn(function()
    local Round = 0
    while true do
        local success, err = pcall(function()
            IsGetKick = checkForError()
            print("Disconnected : ", IsGetKick)
            print("API runs at : ", Round)

            if not IsGetKick then
                updateStatus()
            else
                task.wait(35)
                setOffline()
            end
            
            Round = Round + 1
            print("----------------------------------------")
            task.wait(30)
        end)
        if not success then
            print("Error Caught: ", err) 
        end
    end
end)
