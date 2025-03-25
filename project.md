# Autókereskedés WPF Projekt

Ez a projekt egy egyszerű WPF alkalmazás, amely bemutatja a `TreeView` és `ListView` vezérlők használatát egy autokereskedés kontextusában. A program lehetővé teszi autó márkák és modellek kezelését, valamint új modellek hozzáadását.

## Funkcionalitás

- **TreeView:** Az autó márkák hierarchikus megjelenítése (pl. Toyota, BMW, Volkswagen).
- **ListView:** A kiválasztott márkához tartozó modellek táblázatos megjelenítése (Modell, Évjárat, Ár, Szín).
- **Új modell hozzáadása:** Űrlap segítségével új autó modellek adhatók a kiválasztott márkához.

## Felépítés

### XAML (MainWindow.xaml)
A felület két fő oszlopra oszlik egy `Grid` segítségével:
- **Bal oszlop:** `TreeView` a márkák megjelenítésére.
- **Jobb oszlop:** Egy belső `Grid` két sorral:
  - Felső sor: Űrlap az új modell adatainak megadásához.
  - Alsó sor: `ListView` a modellek listázásához.

Példa a `TreeView` definíciójára:
```c#
<TreeView x:Name="brandTree" Grid.Column="0" Margin="10" 
         SelectedItemChanged="BrandTree_SelectedItemChanged">
    <TreeView.ItemTemplate>
        <DataTemplate>
            <TextBlock Text="{Binding BrandName}"/>
        </DataTemplate>
    </TreeView.ItemTemplate>
</TreeView>
```
Az űrlap és a ListView elrendezése:
```c#
<StackPanel Grid.Row="0" Orientation="Vertical" Margin="0,0,0,10">
    <StackPanel Orientation="Horizontal" Margin="0,0,0,5">
        <TextBlock Text="Modell neve:" Width="80"/>
        <TextBox x:Name="modelNameInput" Width="150"/>
    </StackPanel>
    <!-- További mezők: Évjárat, Ár, Szín -->
    <Button x:Name="addModelButton" Content="Új modell hozzáadása" Width="150" Click="AddModelButton_Click"/>
</StackPanel>
<ListView x:Name="modelList" Grid.Row="1">
    <ListView.View>
        <GridView>
            <GridViewColumn Header="Modell" Width="150" DisplayMemberBinding="{Binding ModelName}"/>
            <!-- További oszlopok: Évjárat, Ár, Szín -->
        </GridView>
    </ListView.View>
</ListView>
```
### C# (MainWindow.xaml.cs)
Adatmodellek: Két osztály definiálja az adatstruktúrát:
```c#
public class CarBrand
{
    public string BrandName { get; set; }
    public List<CarModel> Models { get; set; }
}

public class CarModel
{
    public string ModelName { get; set; }
    public int Year { get; set; }
    public decimal Price { get; set; }
    public string Color { get; set; }
}
```
Főbb metódusok:
InitializeData(): Kezdeti adatok betöltése példa márkákkal és modellekkel.
BrandTree_SelectedItemChanged(): Frissíti a ListView-t:
```c#
private void BrandTree_SelectedItemChanged(object sender, RoutedPropertyChangedEventArgs<object> e)
{
    if (brandTree.SelectedItem is CarBrand selectedBrand)
    {
        modelList.ItemsSource = selectedBrand.Models;
    }
}
```
AddModelButton_Click(): Új modell hozzáadása hibakezeléssel:
```c#
private void AddModelButton_Click(object sender, RoutedEventArgs e)
{
    if (brandTree.SelectedItem is CarBrand selectedBrand)
    {
        if (string.IsNullOrWhiteSpace(modelNameInput.Text) || /* további ellenőrzések */)
        {
            MessageBox.Show("Kérlek, töltsd ki az összes mezőt!", "Hiba", MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }

        if (!int.TryParse(yearInput.Text, out int year) || !decimal.TryParse(priceInput.Text, out decimal price))
        {
            MessageBox.Show("Az évjáratnak és az árnak számnak kell lennie!", "Hiba", MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }

        CarModel newModel = new CarModel
        {
            ModelName = modelNameInput.Text,
            Year = year,
            Price = price,
            Color = colorInput.Text
        };

        selectedBrand.Models.Add(newModel);
        modelList.ItemsSource = null;
        modelList.ItemsSource = selectedBrand.Models;
        // Mezők törlése
    }
}
```
### Használat
1. Indítás: A program betölti az előre definiált márkákat és modelleket.
2. Navigáció: Kattints egy márkára a TreeView-ban, hogy a ListView-ban megjelenjenek a modellek.
3. Új modell hozzáadása:
4. Válassz ki egy márkát.
5. Töltsd ki az űrlapot.
6. Kattints az "Új modell hozzáadása" gombra.

### Telepítés és futtatás
1. Hozz létre egy új WPF projektet Visual Studioban "CarDealership" néven.
2. Másold be a kódot a MainWindow.xaml és MainWindow.xaml.cs fájlokba.
3. Fordítsd le és futtasd.

### Fejlesztési lehetőségek
Színek és stílusok hozzáadása.
Keresőmező a modellek szűrésére.
Adatok mentése fájlba vagy adatbázisba.

<details> <summary>MainWindow.xaml</summary>
```c#
<Window x:Class="project.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:project"
        mc:Ignorable="d"
     Title="Autókereskedés" Height="500" Width="900">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="1*"/>
            <ColumnDefinition Width="3*"/>
        </Grid.ColumnDefinitions>

        <!-- TreeView az autó márkákhoz -->
        <TreeView x:Name="brandTree" Grid.Column="0" Margin="10" 
                 SelectedItemChanged="BrandTree_SelectedItemChanged">
            <TreeView.ItemTemplate>
                <DataTemplate>
                    <TextBlock Text="{Binding BrandName}"/>
                </DataTemplate>
            </TreeView.ItemTemplate>
        </TreeView>

        <!-- Jobb oldali rész: ListView és új modell űrlap -->
        <Grid Grid.Column="1" Margin="10">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <!-- Új modell hozzáadása űrlap -->
            <StackPanel Grid.Row="0" Orientation="Vertical" Margin="0,0,0,10">
                <StackPanel Orientation="Horizontal" Margin="0,0,0,5">
                    <TextBlock Text="Modell neve:" Width="80"/>
                    <TextBox x:Name="modelNameInput" Width="150"/>
                </StackPanel>
                <StackPanel Orientation="Horizontal" Margin="0,0,0,5">
                    <TextBlock Text="Évjárat:" Width="80"/>
                    <TextBox x:Name="yearInput" Width="150"/>
                </StackPanel>
                <StackPanel Orientation="Horizontal" Margin="0,0,0,5">
                    <TextBlock Text="Ár (Ft):" Width="80"/>
                    <TextBox x:Name="priceInput" Width="150"/>
                </StackPanel>
                <StackPanel Orientation="Horizontal" Margin="0,0,0,5">
                    <TextBlock Text="Szín:" Width="80"/>
                    <TextBox x:Name="colorInput" Width="150"/>
                </StackPanel>
                <Button x:Name="addModelButton" Content="Új modell hozzáadása" Width="150" Click="AddModelButton_Click"/>
            </StackPanel>

            <!-- ListView az autó modellekhez -->
            <ListView x:Name="modelList" Grid.Row="1">
                <ListView.View>
                    <GridView>
                        <GridViewColumn Header="Modell" Width="150" DisplayMemberBinding="{Binding ModelName}"/>
                        <GridViewColumn Header="Évjárat" Width="80" DisplayMemberBinding="{Binding Year}"/>
                        <GridViewColumn Header="Ár (Ft)" Width="120" DisplayMemberBinding="{Binding Price}"/>
                        <GridViewColumn Header="Szín" Width="100" DisplayMemberBinding="{Binding Color}"/>
                    </GridView>
                </ListView.View>
            </ListView>
        </Grid>
    </Grid>
</Window>
```
```c#
</details> <details> <summary>MainWindow.xaml.cs</summary>
using System.Collections.ObjectModel;
using System.Text;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;


namespace project
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            InitializeData();
        }

        public class CarBrand
        {
            public string BrandName { get; set; }
            public List<CarModel> Models { get; set; }
        }

        public class CarModel
        {
            public string ModelName { get; set; }
            public int Year { get; set; }
            public decimal Price { get; set; }
            public string Color { get; set; }
        }

        private void InitializeData()
        {
            List<CarBrand> brands = new List<CarBrand>
            {
                new CarBrand
                {
                    BrandName = "Toyota",
                    Models = new List<CarModel>
                    {
                        new CarModel { ModelName = "Corolla", Year = 2022, Price = 8500000, Color = "Fehér" },
                        new CarModel { ModelName = "RAV4", Year = 2023, Price = 12500000, Color = "Fekete" },
                        new CarModel { ModelName = "Yaris", Year = 2021, Price = 6500000, Color = "Kék" }
                    }
                },
                new CarBrand
                {
                    BrandName = "BMW",
                    Models = new List<CarModel>
                    {
                        new CarModel { ModelName = "320d", Year = 2020, Price = 14000000, Color = "Szürke" },
                        new CarModel { ModelName = "X5", Year = 2023, Price = 28000000, Color = "Fekete" },
                        new CarModel { ModelName = "M3", Year = 2022, Price = 32000000, Color = "Piros" }
                    }
                },
                new CarBrand
                {
                    BrandName = "Volkswagen",
                    Models = new List<CarModel>
                    {
                        new CarModel { ModelName = "Golf", Year = 2021, Price = 9500000, Color = "Ezüst" },
                        new CarModel { ModelName = "Tiguan", Year = 2022, Price = 13500000, Color = "Fehér" }
                    }
                }
            };

            brandTree.ItemsSource = brands;
        }

        private void BrandTree_SelectedItemChanged(object sender, RoutedPropertyChangedEventArgs<object> e)
        {
            if (brandTree.SelectedItem is CarBrand selectedBrand)
            {
                modelList.ItemsSource = selectedBrand.Models;
            }
        }

        private void AddModelButton_Click(object sender, RoutedEventArgs e)
        {
            if (brandTree.SelectedItem is CarBrand selectedBrand)
            {
                if (string.IsNullOrWhiteSpace(modelNameInput.Text) ||
                    string.IsNullOrWhiteSpace(yearInput.Text) ||
                    string.IsNullOrWhiteSpace(priceInput.Text) ||
                    string.IsNullOrWhiteSpace(colorInput.Text))
                {
                    MessageBox.Show("Kérlek, töltsd ki az összes mezőt!", "Hiba", MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }

                if (!int.TryParse(yearInput.Text, out int year) || !decimal.TryParse(priceInput.Text, out decimal price))
                {
                    MessageBox.Show("Az évjáratnak és az árnak számnak kell lennie!", "Hiba", MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }

                CarModel newModel = new CarModel
                {
                    ModelName = modelNameInput.Text,
                    Year = year,
                    Price = price,
                    Color = colorInput.Text
                };

                selectedBrand.Models.Add(newModel);
                modelList.ItemsSource = null;
                modelList.ItemsSource = selectedBrand.Models;

                modelNameInput.Text = "";
                yearInput.Text = "";
                priceInput.Text = "";
                colorInput.Text = "";

                MessageBox.Show("Az új modell sikeresen hozzáadva!", "Siker", MessageBoxButton.OK, MessageBoxImage.Information);
            }
            else
            {
                MessageBox.Show("Kérlek, válassz ki egy márkát a felsorolásban!", "Hiba", MessageBoxButton.OK, MessageBoxImage.Warning);
            }
        }
    }
}
```
</details> 