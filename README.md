# Appunti Albyrivo6/ Alberto Rivoira (gitlab)

using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class MappeController : ControllerBase
{
    private readonly MappeService _mappeService;

    public MappeController(MappeService mappeService)
    {
        _mappeService = mappeService;
    }

    [HttpGet]
    public async Task<IActionResult> GetMappe([FromQuery] FiltroMappa filtro)
    {
        var mappe = await _mappeService.GetMappeAsync(filtro);

        // Puoi eventualmente fare mapping a un DTO qui, se non vuoi restituire l'entità EF completa
        return Ok(mappe);
    }
}
