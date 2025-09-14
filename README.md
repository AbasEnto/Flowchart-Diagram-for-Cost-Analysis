# Flowchart-Diagram-for-Cost-Analysis
flowchart TB
  Start([Start])
  Inputs[/User inputs:\n- orchardSystem (traditional/medium/high)\n- orchardAge (age group string)\n- insecticide (name)\n- fixedCostMethod (\"ASA\" | \"Straight-Line\")\n- optional overrides: prices, wages, machine params, fuel price, spray_rate/hrs_per_kanal/repair_rate/interest/TIH/...]/
  Lookup[(Lookup tables & constants:\n- volume_L_per_kanal (Table 3)\n- cost_per_100L (Table 2)\n- dosage_per_100L (Table 2)\n- insecticide_unit_price (Table 1)\n- remaining_value_factors (Annexure)\n- defaults: H (annual hours), L (life yrs), spray_rate_L_per_hr, fuel_l_per_hr, lubrication_pct, repair_pct_per_year, TIH_pct, real_interest_rate, labour_units, wage_per_day, workday_hours)]
  Start --> Inputs --> Lookup

  subgraph Material_Cost_Calc ["Material cost per kanal"]
    M_lookupCost["Get cost_per_100L for selected insecticide\n(cost_per_100L)"]
    M_lookupVolume["Get volume_L_per_kanal for system+age\n(volume_L)"]
    M_materialCalc["MaterialCost_Rs = cost_per_100L * (volume_L / 100)"]
  end

  subgraph Application_Cost_Calc ["Application cost per kanal"]
    A_inputs["Machine params & economic inputs:\nC (purchase), currentListPrice, machineAge, L, H, i, TIH_rate"]
    A_chooseMethod["Choose fixed-cost method\n(ASA or Straight-Line)"]
    A_ASA["ASA method:\nsalvage = currentListPrice * RVF(age)\ndep_per_hr = (C - salvage) / (L * H)\navgInvestment=(C+salvage)/2\ninterest_per_hr = (avgInvestment * i) / H\ntih_per_hr = (avgInvestment * TIH_rate) / H\nfixed_per_hr = dep_per_hr + interest_per_hr + tih_per_hr"]
    A_SL["Straight-Line method:\nsalvage = 0.10 * C\ndep_per_hr = (C - salvage) / (L * H)\navgInvestment=(C+salvage)/2\ninterest_per_hr = (avgInvestment * i) / H\ntih_per_hr = (avgInvestment * TIH_rate) / H\nfixed_per_hr = dep + interest + tih"]
    A_var["Variable costs per hr:\nrepairs_per_hr = (repair_pct_per_year * C) / H\nfuel_per_hr = fuel_l_per_hr * fuel_price_per_L\nlube_per_hr = lubrication_pct * fuel_per_hr\nlabour_per_hr = (numPersons * wage_per_day) / workday_hours"]
    A_totalHourly["total_hourly_cost = fixed_per_hr + variable_per_hr"]
    A_hours["Compute hours_per_kanal:\nOption A: hours_per_kanal = volume_L / spray_rate_L_per_hr\nOption B (document defaults): Type-I => 1 hr, Type-II => 2.25 hr"]
    A_applicationPerKanal["application_cost_per_kanal = total_hourly_cost * hours_per_kanal"]
  end

  Lookup --> M_lookupCost
  Lookup --> M_lookupVolume
  M_lookupCost --> M_materialCalc
  M_lookupVolume --> M_materialCalc

  Lookup --> A_inputs
  A_inputs --> A_chooseMethod
  A_chooseMethod --> A_ASA
  A_chooseMethod --> A_SL
  Lookup --> A_var
  A_ASA --> A_totalHourly
  A_SL --> A_totalHourly
  A_var --> A_totalHourly
  A_totalHourly --> A_hours
  M_materialCalc --> FinalCombine[Combine results]
  A_applicationPerKanal --> FinalCombine
  A_hours --> A_applicationPerKanal
  FinalCombine --> Outputs{{Outputs:\n- Material cost (Rs/kanal)\n- Application cost (Rs/kanal) by method\n- Total cost (Rs/kanal)\n- Detailed breakdown (dep, interest, TIH, repairs, fuel, lube, labour)}}
  Outputs --> End([End])
