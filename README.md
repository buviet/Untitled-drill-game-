local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local OreServiceRE = ReplicatedStorage:WaitForChild("Packages"):WaitForChild("Knit"):WaitForChild("Services"):WaitForChild("OreService"):WaitForChild("RE"):WaitForChild("RequestRandomOre")

-- Create the ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoFarmUI_by_Namharyboss107"
screenGui.Parent = PlayerGui
screenGui.ResetOnSpawn = false

-- Create the main frame
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0.25, 0, 0.35, 0) -- Increased height to accommodate the text
mainFrame.Position = UDim2.new(0.5, -mainFrame.Size.X.Scale / 2, 0.5, -mainFrame.Size.Y.Scale / 2)
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.BackgroundColor3 = Color3.new(0.8, 0.8, 0.8)
mainFrame.BorderColor3 = Color3.new(0, 0, 0)
mainFrame.BorderSizePixel = 2
mainFrame.LayoutOrder = 1

-- Make the main frame draggable
local dragging = false
local dragStart = nil
local initialPosition = nil

mainFrame.InputBegan:Connect(function(input)
  if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
    dragging = true
    dragStart = input.Position
    initialPosition = mainFrame.Position
    input.Changed:Connect(function()
      if input.UserInputState == Enum.UserInputState.End then
        dragging = false
      end
    end)
  end
end)

mainFrame.InputChanged:Connect(function(input)
  if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
    if dragging then
      local delta = input.Position - dragStart
      mainFrame.Position = UDim2.new(
          initialPosition.X.Scale,
          initialPosition.X.Offset + delta.X,
          initialPosition.Y.Scale,
          initialPosition.Y.Offset + delta.Y
      )
    end
  end
end)

mainFrame.Parent = screenGui

-- Create the start button (renamed)
local startButton = Instance.new("TextButton")
startButton.Name = "StartFarmButton"
startButton.Size = UDim2.new(0.9, 0, 0.25, 0)
startButton.Position = UDim2.new(0.05, 0, 0.05, 0)
startButton.Text = "Start Farm"
startButton.BackgroundColor3 = Color3.new(0.6, 0.8, 0.6)
startButton.TextColor3 = Color3.new(0, 0, 0)
startButton.Font = Enum.Font.SourceSansBold
startButton.TextSize = 20
startButton.Parent = mainFrame

-- Create the stop button (renamed)
local stopButton = Instance.new("TextButton")
stopButton.Name = "StopFarmButton"
stopButton.Size = UDim2.new(0.9, 0, 0.25, 0)
stopButton.Position = UDim2.new(0.05, 0, 0.35, 0)
stopButton.Text = "Stop Farm"
stopButton.BackgroundColor3 = Color3.new(0.8, 0.6, 0.6)
stopButton.TextColor3 = Color3.new(0, 0, 0)
stopButton.Font = Enum.Font.SourceSansBold
stopButton.TextSize = 20
stopButton.Parent = mainFrame

-- Create the text label at the bottom
local bottomText = Instance.new("TextLabel")
bottomText.Name = "BottomTextLabel"
bottomText.Size = UDim2.new(1, 0, 0.2, 0)
bottomText.Position = UDim2.new(0, 0, 0.8, 0) -- Positioned at the bottom
bottomText.Text = "Untitled Drill Game\nMade by Namharyboss107"
bottomText.BackgroundColor3 = Color3.new(1, 1, 1)
bottomText.BackgroundTransparency = 1
bottomText.TextColor3 = Color3.new(0, 0, 0)
bottomText.Font = Enum.Font.SourceSans
bottomText.TextSize = 14
bottomText.Parent = mainFrame
bottomText.TextXAlignment = Enum.TextXAlignment.Center -- Center the text horizontally
bottomText.TextYAlignment = Enum.TextYAlignment.Top -- Align text to the top


-- Local variable to control the loop
local isFarming = false
local farmLoop = nil
local farmSpeed = 0.1 -- Set the desired speed here

-- Function to start the ore farming loop (renamed)
local function startOreFarming()
  if not isFarming then
    isFarming = true
    farmLoop = coroutine.create(function()
      while isFarming do
        wait(farmSpeed) -- Use the 'farmSpeed' variable
        -- Check if OreServiceRE exists before firing
        if OreServiceRE then
          OreServiceRE:FireServer()
          print("Requested random ore from server.")
        else
          warn("OreService.RE.RequestRandomOre RemoteEvent not found!")
          isFarming = false -- Stop the loop if the event is missing
        end
      end
      print("Ore farming loop stopped.")
    end)
    coroutine.resume(farmLoop)
    startButton.Text = "Farming..."
    startButton.TextColor3 = Color3.new(0.5, 0.5, 0.5) -- Indicate it's running
    startButton.ButtonState = Enum.ButtonState.Disabled -- Prevent multiple starts
    stopButton.ButtonState = Enum.ButtonState.Enabled
    stopButton.TextColor3 = Color3.new(0, 0, 0)
  end
end

-- Function to stop the ore farming loop (renamed)
local function stopOreFarming()
  if isFarming then
    isFarming = false
    -- The loop will stop on the next iteration due to the 'isFarming' check
    startButton.Text = "Start Farm"
    startButton.TextColor3 = Color3.new(0, 0, 0)
    startButton.ButtonState = Enum.ButtonState.Enabled
    stopButton.ButtonState = Enum.ButtonState.Disabled
    stopButton.TextColor3 = Color3.new(0.5, 0.5, 0.5) -- Indicate it's stopped
  end
end

-- Connect the button click events (renamed functions)
startButton.MouseButton1Click:Connect(startOreFarming)
stopButton.MouseButton1Click:Connect(stopOreFarming)

-- Initially disable the stop button
stopButton.ButtonState = Enum.ButtonState.Disabled
stopButton.TextColor3 = Color3.new(0.5, 0.5, 0.5)
