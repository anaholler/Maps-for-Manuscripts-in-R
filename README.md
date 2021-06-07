# Maps-for-Manuscripts-in-R


## R Spatial Packages

suppressPackageStartupMessages(library(rgdal)) # spatial/shp reading
library(viridis) # nice color palette
library(ggplot2) # plotting
suppressPackageStartupMessages(library(ggmap)) # ggplot functionality for maps
library(ggsn) # for scale bars/north arrows in ggplots
library(maps)
library(mapdata)
suppressPackageStartupMessages(library(cowplot)) ## for arranging ggplots



#a resolução do mapa de adequabilidade é de 4.5km lat, 4.5km lon, ~2.5 min

setwd("C:/Users/Ana Cecilia Holler/Google Drive/UnB/Mestrado - UnB (Zoologia)/Projeto Pithecopus/Modelos Preditivos de distribuição")

#install.packages("raster")
#install.packages("rgdal")
library(raster)

oreades <- raster("P_oreades0.tif")
ayeaye <- raster("P_ayeaye0.tif")




###AYEAYE###
#Abrir arquivo com long,lat salvo em .txt e abrir documento
coord_ayeaye <- read.csv("registros_novos_ayeaye.csv", h=T, sep = ";") #h=primeira linha ler como titulo
head(coord_ayeaye)


#Extrair os pontos
SpatialPoints(coord_ayeaye, proj4string=CRS(as.character(NA)), bbox = NULL) #proj4string=CRS = CRS é o tipo de projeção que eu vou usar
teste = extract(ayeaye,coord_ayeaye) #-> olhar o argumento buffer (função - https://www.rdocumentation.org/packages/raster/versions/3.4-10/topics/extract - fazer um pequeno buffer para se tiver qualquer erro do GPS eu posso incluir esse erro, alguns pixels de distância)
#convertendo, como tabela
out = cbind(coord_ayeaye, teste) #vai dar cada coordenada nova e o valor de cada adequabilidade e ver o quanto foi bom ou não
out
#Criar planilha para os dados de adequabilidade
?write.csv
write.csv(out, file = "Adequabilidade_ayeaye.csv")


#Para ver os pontos no mapa (simples)
plot(ayeaye)
points(coord_ayeaye$lon,coord_ayeaye$lat)
extract(ayeaye, coord_ayeaye, buffer = 10000)

#para ver o histograma
hist(extract(ayeaye, coord_ayeaye))  

#Incluir no histograma os valores de adequabilidade nos pontos visitados, mas sem registro
sem_registro <- read.table("C:/Users/Ana Cecilia Holler/Google Drive/UnB/Mestrado - UnB (Zoologia)/Projeto Pithecopus/Modelos Preditivos de distribuição/sem_registro.txt", h=T)
hist(extract(ayeaye,sem_registro))

##ggplot
library(ggplot2)
nodata <- as.data.frame(cbind(extract(ayeaye,sem_registro),"no"))
data <- as.data.frame(cbind(extract(ayeaye, coord_ayeaye ),"new"))
df=as.data.frame(rbind(data, nodata))
colnames(df)=c("adeq","cat")
df$adeq=as.numeric(df$adeq)
df$cat=as.factor(df$cat)

ggplot(df, aes(x=adeq, color=cat, fill=cat)) +
  geom_histogram(aes(y=..density..), position="identity", alpha=0.5)+
  geom_density(alpha=0.6)+
  scale_color_manual(values=c("#E69F00", "#999999"),labels = c("New records", "No data"))+
  scale_fill_manual(values=c("#E69F00", "#999999"),labels = c("New records", "No data"))+
  labs(x="Suitability", y = "Density")+
  theme(legend.title=element_blank(),
        panel.background = element_blank())





###oreades###
#Abrir arquivo com long,lat salvo em .txt e abrir documento
coord_oreades <- read.csv("registros_novos_oreades.csv", h=T, sep = ";") #h=primeira linha ler como titulo
head(coord_oreades)


#Extrair os pontos
SpatialPoints(coord_oreades, proj4string=CRS(as.character(NA)), bbox = NULL) #proj4string=CRS = CRS é o tipo de projeção que eu vou usar
teste = extract(oreades,coord_oreades) #-> olhar o argumento buffer (função - https://www.rdocumentation.org/packages/raster/versions/3.4-10/topics/extract - fazer um pequeno buffer para se tiver qualquer erro do GPS eu posso incluir esse erro, alguns pixels de distância)
#convertendo, como tabela
out_oreades = cbind(coord_oreades, teste) #vai dar cada coordenada nova e o valor de cada adequabilidade e ver o quanto foi bom ou não
out_oreades
#Criar planilha para os dados de adequabilidade
?write.csv
write.csv(out, file = "Adequabilidade_oreades.csv")


#Para ver os pontos no mapa (simples)
plot(oreades)
points(coord_oreades$lon,coord_oreades$lat)

extract(oreades, coord_oreades, buffer = 10000)

#para ver o histograma
hist(extract(oreades, coord_oreades))  

#Incluir no histograma os valores de adequabilidade nos pontos visitados, mas sem registro
sem_registro <- read.table("C:/Users/Ana Cecilia Holler/Google Drive/UnB/Mestrado - UnB (Zoologia)/Projeto Pithecopus/Modelos Preditivos de distribuição/sem_registro.txt", h=T)
hist(extract(oreades,sem_registro))

##ggplot
library(ggplot2)
nodata <- as.data.frame(cbind(extract(oreades,sem_registro),"no"))
data <- as.data.frame(cbind(extract(oreades, coord_oreades),"new"))
df=as.data.frame(rbind(data, nodata))
colnames(df)=c("adeq","cat")
df$adeq=as.numeric(df$adeq)
df$cat=as.factor(df$cat)

ggplot(df, aes(x=adeq, color=cat, fill=cat)) +
  geom_histogram(aes(y=..density..), position="identity", alpha=0.5)+
  geom_density(alpha=0.6)+
  scale_color_manual(values=c("#E69F00", "#999999"),labels = c("New records", "No data"))+
  scale_fill_manual(values=c("#E69F00", "#999999"),labels = c("New records", "No data"))+
  labs(x="Suitability", y = "Density")+
  theme(legend.title=element_blank(),
        panel.background = element_blank())
