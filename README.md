import csv
import matplotlib.pyplot as plt

# Function to load Global Land Temperature dataset as a list of lists
def load_dataset(filename):
    dataset = []
    with open(filename, 'r') as file:
        csv_reader = csv.reader(file)
        for row in csv_reader:
            dataset.append(row)
    return dataset

# Function to fill missing values in AverageTemperature attribute
def fill_missing_values(dataset):
    for i in range(len(dataset)):
        if dataset[i][1] == '':
            prev_temp = float(dataset[i-1][1]) if i > 0 and dataset[i-1][1] != '' else 0
            next_temp = float(dataset[i+1][1]) if i < len(dataset)-1 and dataset[i+1][1] != '' else 0
            dataset[i][1] = str((prev_temp + next_temp) / 2)
    return dataset

# Function to get top N hottest and coldest measurements for a city
def top_measurements(city, N, dataset):
    city_temps = [(float(row[1]), row[0]) for row in dataset if row[0] == city and row[1] != '']
    city_temps.sort(reverse=True)
    print(f"Top {N} hottest measurements for {city}:")
    for i in range(min(N, len(city_temps))):
        print(f"{i+1}. {city_temps[i][0]}°C")
    print(f"\nTop {N} coldest measurements for {city}:")
    for i in range(min(N, len(city_temps))):
        print(f"{i+1}. {city_temps[-i-1][0]}°C")

# Function to plot histogram of temperature distribution for a city
def plot_temperature_distribution(city, dataset):
    city_temps = [float(row[1]) for row in dataset if row[0] == city and row[1] != '']
    plt.hist(city_temps, bins=20, color='skyblue', edgecolor='black')
    plt.title(f"Temperature Distribution in {city}")
    plt.xlabel("Temperature (°C)")
    plt.ylabel("Frequency")
    plt.show()

# Function to convert Fahrenheit measurements to Celsius
def fahrenheit_to_celsius(temp_f):
    return (temp_f - 32) / 1.8

# Load dataset
dataset = load_dataset('global_land_temperature.csv')

# Fill missing values in AverageTemperature attribute
dataset = fill_missing_values(dataset)

# Print top N hottest and coldest measurements for a city
top_measurements("Rome", 5, dataset)
top_measurements("Bangkok", 5, dataset)

# Plot temperature distribution for Rome and Bangkok
plot_temperature_distribution("Rome", dataset)
plot_temperature_distribution("Bangkok", dataset)

# Convert Fahrenheit measurements to Celsius for Bangkok
for row in dataset:
    if row[0] == "Bangkok" and row[1] != '':
        row[1] = str(fahrenheit_to_celsius(float(row[1])))

# Plot temperature distribution for Rome and Bangkok after conversion
plot_temperature_distribution("Rome", dataset)
plot_temperature_distribution("Bangkok", dataset)
