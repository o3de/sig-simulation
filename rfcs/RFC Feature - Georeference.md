# Support for georefrence

## Summary

This RFC proposes to create a separate canonical gem in o3de-extras repository that will allow georeferencing O3DE level.

## What is the relevance of this feature?

This feature enables advanced robotics cases, simulation, Earth science, geodesy, cartography, and surveying.
It will expose API that will simplify integration with formats like [KML](https://pl.wikipedia.org/wiki/Keyhole_Markup_Language) or [GeoJSON](https://pl.wikipedia.org/wiki/GeoJSON).

## Feature design description

## Technical design description

A new gem called "Georeference" is proposed.
It is implemented and documented as part of ROS 2 gem, but in this RFC I propose to move it to separated canonical gem.
The Gem will contain public API and LevelComponent that will perform conversion calculation. 

In the proposed form, it will allow to perform conversion from [WGS-84](https://en.wikipedia.org/wiki/World_Geodetic_System) to local level's coordinate system and vice-versa. 
Conversion from WGS-84 to Level will be performed via:
- converting WGS-84 coordinates (longitude in degrees, latitude in degrees, altitude in meters) to 3D cartesian coordinate system using WGS-84 ellipsoid reference. New coordinates will be in meters in Earth-centered, [Earth-fixed coordinate system](https://en.wikipedia.org/wiki/Earth-centered,_Earth-fixed_coordinate_system). 
- Apply conversion from ECEF to local ENU(East North Up) frame at given location  [conversion details](https://gssc.esa.int/navipedia/index.php/Transformations_between_ECEF_and_ENU_coordinates).
- Apply affine transform (rotation and translation) to fit configured using GeoReferenceLevelComponent.


### Representation in the engine

The Gem will expose a public API that will allow user to convert [WGS-84](https://en.wikipedia.org/wiki/World_Geodetic_System) coordinates to level and vice versa.
API will in the form of EventBus:
```cpp
 struct WGS84Coordinate
 {
        AZ_RTTI(WGS84Coordinate, "{577a5637-b31a-44c5-a33f-50df2922af2a}");
        static void Reflect(AZ::ReflectContext* context);

        double m_latitude = 0.0; //!< Latitude in degrees.
        double m_longitude = 0.0; //!< Longitude in degrees.
        double m_altitude = 0.0; //!< Altitude in meters.
 };

 //! Interface that allows to convert between level and WGS84 coordinates.
 class GeoreferenceRequests
 {
    public:

        //! Function converts from Level's coordinate system to WGS84.
        //! @param xyz Vector3 in Level's coordinate system.
        //! @return Vector3 in WGS84 coordinate system as @class WGS::WGS84Coordinate.
        virtual WGS::WGS84Coordinate ConvertFromLevelToWGS84(const AZ::Vector3& xyz) = 0;

        //! Function converts from WGS84 coordinate system to Level's.
        //!  @param latLon Vector3 in WGS84 coordinate system, where x is latitude, y is longitude and z is altitude.
        //!  @return Vector3 in Level's coordinate system.
        virtual AZ::Vector3 ConvertFromWGS84ToLevel(const WGS::WGS84Coordinate& latLon) = 0;

        //! Function returns rotation from Level's frame to ENU's (East-North-Up) rotation.
        //! Function is useful to fin georeference rotation of the level.
        //! @return Quaternion in ENU coordinate system.
        virtual AZ::Quaternion GetRotationFromLevelToENU() = 0;
 };
 
 class GeoreferenceRequestsTraits : public AZ::EBusTraits
 {
    public:
        // EBusTraits overrides ...
        static constexpr AZ::EBusHandlerPolicy HandlerPolicy = AZ::EBusHandlerPolicy::Single;
        static constexpr AZ::EBusAddressPolicy AddressPolicy = AZ::EBusAddressPolicy::Single;
 };
```
This Event bus will be handled by a level component.


### New components

**GeoReferenceLevelComponent.**
This component will have a reference to another entity (called `ENU entity`).
`ENU entity` will have:
- known WGS-84 coordinates
- will be oriented in a way to point:
  - X in East direction
  - Y in North direction
  - Z is up.

## What are the advantages of the feature?

- Standard way to convert WGS-84 coordinates to level coordinates system and _vice versa_.
- No need to design the level with the geodetic orientation of the level.

## What are the disadvantages of the feature?

- Adding new gem to maintain.
- It assumes that Earth is flat (locally).
- Will work fine for small (few kilometers level). Larger levels still will need a custom solution.

## Are there any alternatives to this feature?

The current alternative is to keep this feature as part of ROS 2 Gem.

## How will users learn this feature?

Documentation in o3de.org

## Are there any open questions?

- Is the proposed API complete?
- Is this feature is needed outside ROS 2 gem?

---

[Current implementation in ROS2 gem](https://github.com/o3de/o3de-extras/tree/development/Gems/ROS2/Code/Source/Georeference)

[Documentation of the feature in ROS2 gem](https://development--o3deorg.netlify.app/docs/user-guide/interactivity/robotics/georeference/)
