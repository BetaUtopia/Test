# Test



Page

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage
    xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:vm="clr-namespace:Kiosk.ViewModels.Extensions"
    x:Class="Kiosk.Pages.Extensions.KeyboardExtensionsPage"
    x:DataType="vm:KeyboardExtensionsViewModel">
    <ScrollView>
        <VerticalStackLayout Spacing="12">
            <Label Text=""
                   HorizontalOptions="Center"
                   HorizontalTextAlignment="Center"/>

            <Label Text="{Binding operationResult}"/>


            <Button Text="Update Label" Clicked="OnUpdateLabelClicked" />
            <Label x:Name="testLabel" />

            <Entry Text="" x:Name="entryToFocus" Focused="OnEntryFocused"/>
            <Entry x:Name="testEntry" />
            <Button Text="Show Keyboard" Clicked="OnShowKeyboardClicked" />
            
            <Button Text="Show Keyboard" CommandParameter="{x:Reference Name=entryToFocus}" Command="{Binding ShowKeyboardCommand}"/>
            <Button Text="Hide Keyboard" CommandParameter="{x:Reference Name=entryToFocus}" Command="{Binding HideKeyboardCommand}"/>
            <Button Text="Check Keyboard State" CommandParameter="{x:Reference Name=entryToFocus}" Command="{Binding IsKeyboardShowingCommand}"/>
        </VerticalStackLayout>
    </ScrollView>
</ContentPage>
```

Class
```c#
using CommunityToolkit.Maui.Core.Platform;
using Kiosk.ViewModels.Extensions;

namespace Kiosk.Pages.Extensions;

public partial class KeyboardExtensionsPage : ContentPage
{
    public KeyboardExtensionsPage(KeyboardExtensionsViewModel viewModel)
    {
        InitializeComponent();
        BindingContext = viewModel; // Set the BindingContext
    }

    void OnEntryFocused(System.Object sender, Microsoft.Maui.Controls.FocusEventArgs e)
    {
    }

    private async void OnShowKeyboardClicked(object sender, EventArgs e)
    {
        await Task.Delay(100);
        await testEntry.ShowKeyboardAsync(CancellationToken.None);
    }

    private void OnUpdateLabelClicked(object sender, EventArgs e)
    {
        testLabel.Text = "Label Updated!";
    }

}
```

Model
```c#
using CommunityToolkit.Maui.Core.Platform;
using CommunityToolkit.Mvvm.ComponentModel;
using CommunityToolkit.Mvvm.Input;
using System.ComponentModel;

namespace Kiosk.ViewModels.Extensions;


[ObservableObject]
public partial class KeyboardExtensionsViewModel 
{

    [ObservableProperty]
    public string operationResult = string.Empty;

    [RelayCommand]
    void OnIsKeyboardShowing(ITextInput view)
    {
        if (view.IsSoftKeyboardShowing())
        {
            operationResult = $"Soft Input Is Currently Showing";
        }
        else
        {
            operationResult = $"Soft Input Is Currently Hidden";
        }
    }

    [RelayCommand]
    async Task OnHideKeyboard(ITextInput view, CancellationToken token)
    {
        try
        {
            bool isSuccessful = await view.HideKeyboardAsync(token);

            if (isSuccessful)
            {
                operationResult = "Hide Succeeded";
            }
            else
            {
                operationResult = "Hide Failed";
            }
        }
        catch (Exception e)
        {
            operationResult = e.Message;
        }
    }

    [RelayCommand]
    async Task OnShowKeyboard(ITextInput view, CancellationToken token)
    {
        Console.WriteLine("Show Keyboard command executed.");
        try
        {
            bool isSuccessful = await view.ShowKeyboardAsync(token);

            if (isSuccessful)
            {
                operationResult = "Show Succeeded";
                Console.WriteLine("Show Succeeded");
            }
            else
            {
                operationResult = "Show Failed";
                Console.WriteLine("Show Failed");
            }
        }
        catch (Exception e)
        {
            operationResult = e.Message;
            Console.WriteLine($"Error: {e.Message}");
        }

        Console.WriteLine($"OperationResult: {operationResult}");
    }


}
```


